# Como criar e configurar o Crawler

## Criação do Glue Crawler 
democratizacao-funcionarios-crawler

![image](https://github.com/user-attachments/assets/712d93f8-c173-42e0-8793-6923b38d0b07)

-  Fonte de Dados - s3://vitor-democratizacao/funcionarios/
- IAM Role - AWSGlueServiceRole-glue-democratizacao-role
  
![image](https://github.com/user-attachments/assets/4285e727-79e3-4ea0-8635-b04c5a671bf1)

-  Destino - democratizacao_db

  ![image](https://github.com/user-attachments/assets/0c34afbf-4964-4e9f-b94f-c72dae479d37)
 ###### OBS: tabela cargos criada posderiomente para join do athena.

- Tabela Funcionarios

![image](https://github.com/user-attachments/assets/34944a4f-c9f4-4bf0-9e81-9a089928468a)

- Telaba Cargos

![image](https://github.com/user-attachments/assets/fdbdd6b2-64ba-4d18-8cdb-b72129d25779)
###### OBS: Tebala cargos foi feita apatir de uma arquivo .csv para agilizar e não criar outra lambda para incluir os dados no S3.
