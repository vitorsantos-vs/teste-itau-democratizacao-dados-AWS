# Código .NET, build, deploy e configuração da função

## 1 - Criação do Lambda para .NET.
### rds-to-s3-parquet
- Criação das variáveis de ambiente:
```
DB_CONNECTION_STRING : Server=democratizacao-db.c8xy4kcoa4rk.us-east-1.rds.amazonaws.com;Database=democratizacao_db;Uid=admin;Pwd=awsrdsmysql
```
```
S3_BUCKET_NAME : vitor-democratizacao
```
![image](https://github.com/user-attachments/assets/dd6fe6d0-ad68-48f0-9c26-32e318285d35)

- Configuração VPC
  
democratizacao-vpc2

sub-redes privadas

grupos de segurança - lambda-sg

![image](https://github.com/user-attachments/assets/71ef5564-c522-4b2a-bd99-4da035534f65)

## 2 - Teste dos primeiros dados.

![image](https://github.com/user-attachments/assets/cc974a40-b29a-487f-b764-cb42f865cffb)
#
![image](https://github.com/user-attachments/assets/035cccee-714a-40d5-b61a-5e71a0d24789)

## 3 - Criação do código .NET e pacotes necessários.
- Pacotes
```
Amazon.Lambda.Core - Version="2.6.0" 
Amazon.Lambda.Serialization.SystemTextJson - Version="2.4.4" 
AWSSDK.S3 - Version="4.0.4.1" 
MySql.Data - Version="9.3.0" 
Parquet.Net - Version="3.15.4" 
```
- Codigo
```csharp
using Amazon.Lambda.Core;
using Amazon.S3;
using Amazon.S3.Model;
using MySql.Data.MySqlClient;
using Parquet;
using Parquet.Data;
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

[assembly: LambdaSerializer(typeof(Amazon.Lambda.Serialization.SystemTextJson.DefaultLambdaJsonSerializer))]

namespace RdsToS3Parquet;

public class Function
{
    public class Funcionario
    {
        public int Id { get; set; }
        public string? Nome { get; set; }
        public int? CargoId { get; set; }
        public DateTime? DataContratacao { get; set; }
        public DateTime DataReferencia { get; set; }
    }

    private readonly IAmazonS3 _s3Client;

    public Function()
    {
        _s3Client = new AmazonS3Client();
    }

    public async Task<string> FunctionHandler(ILambdaContext context)
    {
        var dbConnectionString = Environment.GetEnvironmentVariable("DB_CONNECTION_STRING");
        var bucketName = Environment.GetEnvironmentVariable("S3_BUCKET_NAME");

        if (string.IsNullOrEmpty(dbConnectionString) || string.IsNullOrEmpty(bucketName))
        {
            var errorMessage = "Erro: Variáveis de ambiente DB_CONNECTION_STRING e/ou S3_BUCKET_NAME não estão definidas.";
            context.Logger.LogError(errorMessage);
            return errorMessage;
        }

        var dataReferencia = DateTime.UtcNow.Date;
        var funcionarios = new List<Funcionario>();

        try
        {
            using var connection = new MySqlConnection(dbConnectionString);
            await connection.OpenAsync();

            var sql = "SELECT id, nome, cargo_id, data_contratacao, data_referencia FROM funcionarios WHERE data_referencia = @data_referencia";
            using var command = new MySqlCommand(sql, connection);
            command.Parameters.AddWithValue("@data_referencia", dataReferencia);

            using var reader = await command.ExecuteReaderAsync();
            while (await reader.ReadAsync())
            {
                funcionarios.Add(new Funcionario
                {
                    Id = reader.GetInt32("id"),
                    Nome = reader.IsDBNull(reader.GetOrdinal("nome")) ? null : reader.GetString("nome"),
                    CargoId = reader.IsDBNull(reader.GetOrdinal("cargo_id")) ? null : reader.GetInt32("cargo_id"),
                    DataContratacao = reader.IsDBNull(reader.GetOrdinal("data_contratacao")) ? null : reader.GetDateTime("data_contratacao"),
                    DataReferencia = reader.GetDateTime("data_referencia")
                });
            }

            context.Logger.LogInformation($"{funcionarios.Count} registros encontrados para {dataReferencia:yyyy-MM-dd}.");
        }
        catch (Exception ex)
        {
            context.Logger.LogError($"Erro ao acessar o banco: {ex.Message}");
            return $"Erro no banco: {ex.Message}";
        }

        if (funcionarios.Count == 0)
        {
            context.Logger.LogInformation("Nenhum dado encontrado para a data de referência.");
            return "Nenhum dado encontrado.";
        }

        using var memoryStream = new MemoryStream();
        try
        {
            
            var schema = new Schema(
                new DataField<int>("id"),
                new DataField<string>("nome"),
                new DataField<int?>("cargo_id"),
                new DataField<DateTimeOffset?>("data_contratacao"),
                new DataField<DateTimeOffset>("data_referencia")
            );

            using (var parquetWriter = await ParquetWriter.CreateAsync(schema, memoryStream))
            {
                using (var groupWriter = parquetWriter.CreateRowGroup())
                {
                    await groupWriter.WriteColumnAsync(new Parquet.Data.DataColumn(
                        (Parquet.Data.DataField)schema.Fields[0],
                        funcionarios.Select(f => f.Id).ToArray()
                    ));
                    await groupWriter.WriteColumnAsync(new Parquet.Data.DataColumn(
                        (Parquet.Data.DataField)schema.Fields[1],
                        funcionarios.Select(f => f.Nome).ToArray()
                    ));
                    await groupWriter.WriteColumnAsync(new Parquet.Data.DataColumn(
                        (Parquet.Data.DataField)schema.Fields[2],
                        funcionarios.Select(f => f.CargoId).ToArray()
                    ));
                    await groupWriter.WriteColumnAsync(new Parquet.Data.DataColumn(
                        (Parquet.Data.DataField)schema.Fields[3],
                        funcionarios.Select(f => f.DataContratacao.HasValue
                            ? (DateTimeOffset?)new DateTimeOffset(f.DataContratacao.Value)
                            : null).ToArray()
                    ));

                    await groupWriter.WriteColumnAsync(new Parquet.Data.DataColumn(
                        (Parquet.Data.DataField)schema.Fields[4],
                        funcionarios.Select(f => new DateTimeOffset(f.DataReferencia)).ToArray()
                    ));
                }
            }

            context.Logger.LogInformation("Parquet gerado com sucesso.");
        }
        catch (Exception ex)
        {
            context.Logger.LogError($"Erro ao gerar Parquet: {ex.Message}");
            return $"Erro no Parquet: {ex.Message}";
        }

        var s3Key = $"funcionarios/ano={dataReferencia:yyyy}/mes={dataReferencia:MM}/dia={dataReferencia:dd}/data.parquet";

        try
        {
            memoryStream.Position = 0;
            var putRequest = new PutObjectRequest
            {
                BucketName = bucketName,
                Key = s3Key,
                InputStream = memoryStream
            };

            await _s3Client.PutObjectAsync(putRequest);
            context.Logger.LogInformation($"Arquivo salvo: s3://{bucketName}/{s3Key}");
        }
        catch (Exception ex)
        {
            context.Logger.LogError($"Erro ao salvar no S3: {ex.Message}");
            return $"Erro no S3: {ex.Message}";
        }

        return $"Sucesso! {funcionarios.Count} registros salvos em s3://{bucketName}/{s3Key}";
    }
}
```

