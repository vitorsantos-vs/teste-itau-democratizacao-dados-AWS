# Criação de Schedule para o Lambda excutar diáriamente.

## Criação de agendamento no EventBrige para excutar o Lambda diáriamente às 12:15 (horário de Brasília)

- Definimos a Frequência: Usamos uma expressão cron é (15 15 * * ? * (porque o cron usa o horário UTC, que é +3h)).
- Permissão: Para Deixar o próprio EventBridge criar a permissão (IAM Role) que ele precisa para poder "chamar" a nossa Lambda.

executa-lambda-diariamente-12h

![image](https://github.com/user-attachments/assets/9377b9d6-48d1-4e21-b6ef-0e7c3769e3c0)

Cronograma

![image](https://github.com/user-attachments/assets/021eb22e-a9b8-495f-9f6e-885534518e32)

Destino

![image](https://github.com/user-attachments/assets/59622723-d5c4-45f0-8896-951e9a9c7d08)

###### executa-etl-carga-full

![image](https://github.com/user-attachments/assets/18aaadbe-1a1c-41ef-b362-446df879ef29)
