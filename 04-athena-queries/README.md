# Configuração do Athena e queries de validação

## 1 - Criação de Bucket no S3 para armazenar os resultatos.
- athena-results

![image](https://github.com/user-attachments/assets/835eed20-e5d6-45e3-b674-6b4bd598f26c)

- Consulta com os dados agregados com todas a informações

```sql
SELECT f.nome AS funcionario,
	f.data_contratacao,
	f.data_referencia,
	c.nome AS cargo,
	c.salario,
	c.bonus
FROM funcionarios f
	JOIN cargos c ON f.cargo_id = c.id
ORDER BY f.nome;
```
   
![image](https://github.com/user-attachments/assets/b2d6666a-fa65-48d7-ba13-7b5172cb2c84)

- Consulta com os dados por Nome, Cargo e Salário.

```sql
SELECT
  f.nome AS nome_funcionario,
  c.nome AS nome_cargo,
  c.salario
FROM "democratizacao_db"."funcionarios" f
JOIN "democratizacao_db"."cargos" c
  ON f.cargo_id = c.id
WHERE f.ano = '2025' AND f.mes = '07' 
LIMIT 100;
```

![image](https://github.com/user-attachments/assets/c92bdfaa-ff1c-47ed-bf9a-708068382962)

###### (JOIN com Cargos): Para fazer uma análise mais completa, precisávamos cruzar os dados de funcionarios com os de cargos. Para isso, fizemos um "mini-processo":

###### Criamos um arquivo cargos.csv.

###### Fizemos o upload dele para a pasta s3://vitor-democratizacao/cargos/.

###### Rodamos um novo Crawler do Glue só para essa pasta, que criou a tabela cargos.

###### Com as duas tabelas no catálogo, rodamos uma query com JOIN para juntar tudo.

###### * Comentado na 03-glue-crawler
