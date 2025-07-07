# Criação do banco, scripts SQL para tabelas e população de dados

## 1 - Criação do RDS MySQL.
### democratizacao-db
- criação do Security Groups - lambda-sg
- Inbound Rule - rds-sg
- criação da vpc

![image](https://github.com/user-attachments/assets/58297833-1097-42c1-b86d-4bd9c2b8caad)
#
![image](https://github.com/user-attachments/assets/449e7330-c6ce-4951-ac19-07af9e71d3ff)

## 2 - Criação do Bucket S3.
### vitor-democratizacao
- Pasta funcionarios
- Pasta Cargos
- Pasta athena-results (Para os resultados das consultas do Athena)

![image](https://github.com/user-attachments/assets/f81dfc4b-7782-4c93-9981-6793cf7f64f7)

### 3 - Criação do Scripts SQL (Criação e População do RDS).

## Criação do Banco e Tabela

```sql
CREATE DATABASE IF NOT EXISTS democratizacao_db;
USE democratizacao_db;

CREATE TABLE cargos (
    id INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    salario DECIMAL(12,2) NOT NULL,
    bonus DECIMAL(13,2)
);

CREATE TABLE funcionarios (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255),
    cargo_id INT,
    data_contratacao DATE,
    data_referencia DATE NOT NULL,
    FOREIGN KEY (cargo_id) REFERENCES cargos(id)
);
```

## Criação dos primeiro dados de teste

```sql
USE democratizacao_db;

-- Inserir Cargos
INSERT INTO cargos (id, nome, salario, bonus) VALUES
(1, 'Engenheiro de Software Jr', 5000.00, 1000.00),
(2, 'Engenheiro de Software Pl', 8500.00, 2500.00),
(3, 'Engenheiro de Software Sr', 12000.00, 5000.00),
(4, 'Analista de Dados', 7000.00, 2000.00),
(5, 'Cientista de Dados', 11000.00, 4500.00),
(6, 'Gerente de Projetos', 13000.00, 6000.00);

-- Inserir Funcionários
INSERT INTO funcionarios (nome, cargo_id, data_contratacao, data_referencia) VALUES
('Ana Silva', 1, '2023-01-15', CURDATE()), ('Bruno Costa', 2, '2022-05-20', CURDATE()),
('Carla Dias', 3, '2021-11-10', CURDATE()), ('Daniel Faria', 4, '2023-02-01', CURDATE()),
('Eduarda Lima', 5, '2022-08-30', CURDATE()), ('Fábio Martins', 6, '2020-03-12', CURDATE()),
('Gabriela Nunes', 1, '2023-03-18', CURDATE()), ('Heitor Oliveira', 2, '2022-07-22', CURDATE()),
('Isabela Pereira', 3, '2021-09-05', CURDATE()), ('João Ramos', 4, '2023-04-11', CURDATE()),
('Larissa Santos', 5, '2022-10-14', CURDATE()), ('Marcos Souza', 6, '2020-01-20', CURDATE()),
('Natália Almeida', 1, '2023-05-25', CURDATE()), ('Otávio Barbosa', 2, '2022-09-28', CURDATE()),
('Patrícia Carvalho', 3, '2021-07-15', CURDATE()), ('Rafael Ferreira', 4, '2023-06-30', CURDATE()),
('Sofia Gomes', 5, '2022-12-01', CURDATE()), ('Tiago Mendes', 1, '2023-07-07', CURDATE()),
('Úrsula Pinto', 2, '2022-11-11', CURDATE()), ('Victor Ribeiro', 3, '2021-05-21', CURDATE()),
('Yasmin Rocha', 4, '2023-08-14', CURDATE()), ('Zeca Vieira', 5, '2022-02-18', CURDATE()),
('Amanda Azevedo', 6, '2019-12-01', CURDATE()), ('Bento Cunha', 1, '2023-09-01', CURDATE()),
('Celina Esteves', 2, '2022-01-10', CURDATE()), ('Davi Lemos', 3, '2021-03-25', CURDATE()),
('Elisa Matos', 4, '2023-10-05', CURDATE()), ('Frederico Neves', 5, '2022-04-15', CURDATE()),
('Giovana Pires', 1, '2023-11-12', CURDATE()), ('Hugo Queiroz', 2, '2022-06-20', CURDATE()),
('Íris Sampaio', 3, '2021-02-08', CURDATE()), ('Jonas Tavares', 4, '2023-12-18', CURDATE()),
('Lia Vasconcelos', 5, '2022-08-01', CURDATE()), ('Mauro Xavier', 6, '2019-10-10', CURDATE()),
('Noah Arantes', 1, '2024-01-05', CURDATE()), ('Olívia Bastos', 2, '2023-03-15', CURDATE()),
('Pietro Campos', 3, '2022-05-19', CURDATE()), ('Raquel Dantas', 4, '2024-02-02', CURDATE()),
('Samuel Eanes', 5, '2023-07-28', CURDATE()), ('Tainá Furtado', 1, '2024-03-01', CURDATE()),
('Uriel Galvão', 2, '2023-09-11', CURDATE()), ('Valentina Henriques', 3, '2022-11-22', CURDATE()),
('Theo Iglésias', 4, '2024-04-04', CURDATE()), ('Stella Jardim', 5, '2023-01-20', CURDATE()),
('Benjamin Klein', 6, '2019-08-08', CURDATE()), ('Alice Lopes', 1, '2024-05-01', CURDATE()),
('Gael Morais', 2, '2023-02-14', CURDATE()), ('Helena Nogueira', 3, '2022-10-30', CURDATE()),
('Miguel Otero', 4, '2024-06-05', CURDATE()), ('Laura Paiva', 5, '2023-04-19', CURDATE());
```

## Criação dos segundo dados de teste (para testa o eventbrige)

```sql
INSERT INTO funcionarios (nome, cargo_id, data_contratacao, data_referencia) VALUES
('Ana Silva', 1, '2023-01-15', CURDATE()), ('Bruno Costa', 2, '2022-05-20', CURDATE()),
('Carla Dias', 3, '2021-11-10', CURDATE()), ('Daniel Faria', 4, '2023-02-01', CURDATE()),
('Eduarda Lima', 5, '2022-08-30', CURDATE()), ('Fábio Martins', 6, '2020-03-12', CURDATE()),
('Gabriela Nunes', 1, '2023-03-18', CURDATE()), ('Heitor Oliveira', 2, '2022-07-22', CURDATE()),
('Isabela Pereira', 3, '2021-09-05', CURDATE()), ('João Ramos', 4, '2023-04-11', CURDATE()),
('Larissa Santos', 5, '2022-10-14', CURDATE()), ('Marcos Souza', 6, '2020-01-20', CURDATE()),
('Natália Almeida', 1, '2023-05-25', CURDATE()), ('Otávio Barbosa', 2, '2022-09-28', CURDATE()),
('Patrícia Carvalho', 3, '2021-07-15', CURDATE()), ('Rafael Ferreira', 4, '2023-06-30', CURDATE()),
('Sofia Gomes', 5, '2022-12-01', CURDATE()), ('Tiago Mendes', 1, '2023-07-07', CURDATE()),
('Úrsula Pinto', 2, '2022-11-11', CURDATE()), ('Victor Ribeiro', 3, '2021-05-21', CURDATE()),
('Yasmin Rocha', 4, '2023-08-14', CURDATE()), ('Zeca Vieira', 5, '2022-02-18', CURDATE()),
('Amanda Azevedo', 6, '2019-12-01', CURDATE()), ('Bento Cunha', 1, '2023-09-01', CURDATE()),
('Celina Esteves', 2, '2022-01-10', CURDATE()), ('Davi Lemos', 3, '2021-03-25', CURDATE()),
('Elisa Matos', 4, '2023-10-05', CURDATE()), ('Frederico Neves', 5, '2022-04-15', CURDATE()),
('Giovana Pires', 1, '2023-11-12', CURDATE()), ('Hugo Queiroz', 2, '2022-06-20', CURDATE()),
('Íris Sampaio', 3, '2021-02-08', CURDATE()), ('Jonas Tavares', 4, '2023-12-18', CURDATE()),
('Lia Vasconcelos', 5, '2022-08-01', CURDATE()), ('Mauro Xavier', 6, '2019-10-10', CURDATE()),
('Noah Arantes', 1, '2024-01-05', CURDATE()), ('Olívia Bastos', 2, '2023-03-15', CURDATE()),
('Pietro Campos', 3, '2022-05-19', CURDATE()), ('Raquel Dantas', 4, '2024-02-02', CURDATE()),
('Samuel Eanes', 5, '2023-07-28', CURDATE()), ('Tainá Furtado', 1, '2024-03-01', CURDATE()),
('Uriel Galvão', 2, '2023-09-11', CURDATE()), ('Valentina Henriques', 3, '2022-11-22', CURDATE()),
('Theo Iglésias', 4, '2024-04-04', CURDATE()), ('Stella Jardim', 5, '2023-01-20', CURDATE()),
('Benjamin Klein', 6, '2019-08-08', CURDATE()), ('Alice Lopes', 1, '2024-05-01', CURDATE()),
('Gael Morais', 2, '2023-02-14', CURDATE()), ('Helena Nogueira', 3, '2022-10-30', CURDATE()),
('Miguel Otero', 4, '2024-06-05', CURDATE()), ('Laura Paiva', 5, '2023-04-19', CURDATE()),
('Alan Torres', 1, '2023-01-08', CURDATE()), ('Beatriz Andrade', 2, '2022-05-03', CURDATE()),
('Caio Lima', 3, '2021-10-20', CURDATE()), ('Denise Reis', 4, '2023-02-25', CURDATE()),
('Emanuel Brito', 5, '2022-09-15', CURDATE()), ('Flávia Cunha', 6, '2020-04-28', CURDATE()),
('Gerson Ribeiro', 1, '2023-03-12', CURDATE()), ('Helena Duarte', 2, '2022-06-05', CURDATE()),
('Igor Fonseca', 3, '2021-08-30', CURDATE()), ('Juliana Amorim', 4, '2023-04-22', CURDATE()),
('Kelvin Santos', 5, '2022-11-07', CURDATE()), ('Lívia Moreira', 6, '2020-02-19', CURDATE()),
('Matheus Pacheco', 1, '2023-05-31', CURDATE()), ('Natalie Castro', 2, '2022-07-17', CURDATE()),
('Otávio Leite', 3, '2021-06-02', CURDATE()), ('Priscila Guerra', 4, '2023-06-18', CURDATE()),
('Renan Correia', 5, '2022-12-03', CURDATE()), ('Sara Viana', 6, '2020-06-15', CURDATE()),
('Thiago Rocha', 1, '2023-07-20', CURDATE()), ('Ursula Mendes', 2, '2022-10-26', CURDATE()),
('Vinícius Cardoso', 3, '2021-04-10', CURDATE()), ('Waleska Nunes', 4, '2023-08-30', CURDATE()),
('Xavier Sampaio', 5, '2022-03-11', CURDATE()), ('Yuri Carvalho', 6, '2019-12-05', CURDATE()),
('Amanda Pires', 1, '2023-09-22', CURDATE()), ('Bruno Silveira', 2, '2022-01-09', CURDATE()),
('Clara Farias', 3, '2021-02-23', CURDATE()), ('Diego Costa', 4, '2023-10-12', CURDATE()),
('Eliane Teles', 5, '2022-04-28', CURDATE()), ('Fernando Martins', 6, '2020-08-03', CURDATE()),
('Gabriel Mello', 1, '2023-11-15', CURDATE()), ('Hanna Vasquez', 2, '2022-06-30', CURDATE()),
('Ivan Gonçalves', 3, '2021-01-14', CURDATE()), ('Jéssica Fernandes', 4, '2023-12-05', CURDATE()),
('Kleber Azevedo', 5, '2022-08-21', CURDATE()), ('Larissa Antunes', 6, '2020-01-30', CURDATE()),
('Manuela Braga', 1, '2024-01-17', CURDATE()), ('Nelson Duarte', 2, '2023-03-06', CURDATE()),
('Orlando Paes', 3, '2022-05-24', CURDATE()), ('Patrícia Lima', 4, '2024-02-22', CURDATE()),
('Rafaela Lopes', 5, '2023-07-03', CURDATE()), ('Samuel Pinheiro', 1, '2024-03-08', CURDATE()),
('Talita Silva', 2, '2023-09-16', CURDATE()), ('Ulisses Moura', 3, '2022-11-28', CURDATE()),
('Valéria Prado', 4, '2024-04-15', CURDATE()), ('Wellington Dias', 5, '2023-01-27', CURDATE()),
('Ximena Barbosa', 6, '2019-10-20', CURDATE()), ('Yasmin Lira', 1, '2024-05-05', CURDATE()),
('Zé Ricardo', 2, '2023-02-10', CURDATE()), ('Agatha Cardoso', 3, '2022-09-02', CURDATE()),
('Bernardo Sales', 4, '2024-06-11', CURDATE()), ('Camila Bezerra', 5, '2023-03-23', CURDATE()),
('David Silva', 6, '2019-07-30', CURDATE()), ('Ester Neves', 1, '2024-07-01', CURDATE()),
('Felipe Costa', 2, '2023-04-14', CURDATE()), ('Glória Almeida', 3, '2022-10-08', CURDATE()),
('Hugo Lima', 4, '2024-08-06', CURDATE()), ('Ingrid Tavares', 5, '2023-05-13', CURDATE()),
('Jorge Nunes', 6, '2020-05-25', CURDATE()), ('Karen Andrade', 1, '2024-09-03', CURDATE()),
('Leonardo Pinto', 2, '2023-06-27', CURDATE()), ('Marta Oliveira', 3, '2022-02-04', CURDATE()),
('Nayara Cruz', 4, '2024-10-10', CURDATE()), ('Oscar Ribeiro', 5, '2023-08-19', CURDATE()),
('Paula Martins', 6, '2019-09-14', CURDATE()), ('Quésia Lopes', 1, '2024-11-12', CURDATE()),
('Rodrigo Dias', 2, '2023-10-01', CURDATE()), ('Simone Freitas', 3, '2022-07-08', CURDATE()),
('Túlio Garcia', 4, '2024-12-02', CURDATE()), ('Vivian Cunha', 5, '2023-02-05', CURDATE()),
('Wallace Rocha', 6, '2020-03-01', CURDATE()), ('Xuxa Fernandes', 1, '2025-01-01', CURDATE()),
('Yasmin Prado', 2, '2023-11-28', CURDATE()), ('Zeca Barreto', 3, '2022-06-18', CURDATE());
```

## Criação dos terceiro dados de teste (para teste do eventbrige diário)

```sql
INSERT INTO funcionarios (nome, cargo_id, data_contratacao, data_referencia) VALUES
('Adriana Rocha', 4, '2023-09-18', CURDATE()), ('Bernardo Cunha', 2, '2022-03-29', CURDATE()),
('Camila Fernandes', 5, '2021-06-12', CURDATE()), ('Douglas Nogueira', 3, '2020-11-03', CURDATE()),
('Eduardo Bastos', 1, '2024-02-20', CURDATE()), ('Fernanda Vasconcelos', 6, '2019-08-19', CURDATE()),
('Gustavo Mendes', 2, '2023-05-14', CURDATE()), ('Heloísa Martins', 5, '2022-07-09', CURDATE()),
('Isadora Guimarães', 1, '2023-10-27', CURDATE()), ('João Pedro Braga', 3, '2021-01-16', CURDATE()),
('Karen Souza', 4, '2024-04-03', CURDATE()), ('Leonardo Almeida', 6, '2020-02-28', CURDATE()),
('Marina Cavalcante', 2, '2023-07-31', CURDATE()), ('Natanael Lima', 5, '2022-09-23', CURDATE()),
('Olívia Moura', 3, '2021-04-06', CURDATE()), ('Pedro Henrique Moraes', 1, '2024-05-19', CURDATE()),
('Quezia Sampaio', 6, '2019-10-02', CURDATE()), ('Roberta Andrade', 2, '2023-03-07', CURDATE()),
('Samuel Andrade', 4, '2022-05-30', CURDATE()), ('Talita Barbosa', 5, '2021-09-11', CURDATE()),
('Ulisses Prado', 3, '2020-06-22', CURDATE()), ('Vanessa Cardoso', 1, '2024-06-16', CURDATE()),
('Wagner Silva', 6, '2019-11-27', CURDATE()), ('Xênia Freitas', 2, '2023-08-08', CURDATE()),
('Ygor Teixeira', 5, '2022-01-19', CURDATE()), ('Zilda Mendes', 4, '2021-12-05', CURDATE())
```
