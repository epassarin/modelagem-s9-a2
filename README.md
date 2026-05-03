# modelagem-s9-a2
Aula pratica modelagem Cláusulas SELECT avançadas  
***
***

# Banco de Dados – SQL

## Atividade Prática: Desenvolvendo consultas para uma empresa de logística

### **Solução COMPLETA, comentada e detalhada**

**Nome:** \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  
**Turma:** \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

***

## 1️⃣ Objetivo da Atividade

O objetivo desta atividade é aplicar **consultas SQL com SELECT avançado**, utilizando:

*   `WHERE` para filtragem de linhas;
*   `GROUP BY` para agrupamento de dados;
*   `HAVING` para filtragem após agregação;
*   `JOIN` para relacionar tabelas;
*   funções de agregação (`SUM`).

O cenário simula uma **empresa de logística**, analisando entregas e veículos para apoiar decisões operacionais.

***

## 2️⃣ Preparação do Ambiente (Passo a Passo)

### ✅ IDE / Programa recomendado

Utilize o **MySQL Workbench**, pois:

*   é amplamente utilizado em cursos técnicos;
*   possui boa interface gráfica;
*   compatível com todos os comandos utilizados.

### ✅ Instalação

1.  Acesse: <https://dev.mysql.com/downloads/>
2.  Instale:
    *   MySQL Server
    *   MySQL Workbench
3.  Abra o MySQL Workbench e conecte:
    *   Host: `localhost`
    *   Usuário: `root`
    *   Senha: definida na instalação

***

## 3️⃣ Criação do Banco de Dados

```sql
CREATE DATABASE logistica;
USE logistica;
```

***

## 4️⃣ Criação das Tabelas

### 🚚 Tabela: Veiculos

```sql
CREATE TABLE Veiculos (
  id_veiculo INT PRIMARY KEY,
  modelo VARCHAR(100),
  ano INT,
  capacidade_carga DECIMAL(5,2)
);
```

### Inserção de dados

```sql
INSERT INTO Veiculos (id_veiculo, modelo, ano, capacidade_carga) VALUES
(1, 'Caminhão A', 2018, 10.0),
(2, 'Caminhão B', 2020, 15.0),
(3, 'Van X', 2015, 3.0),
(4, 'Caminhão C', 2022, 12.0);
```

***

### 📦 Tabela: Entregas

```sql
CREATE TABLE Entregas (
  id_entrega INT PRIMARY KEY,
  id_veiculo INT,
  data_entrega DATE,
  cidade VARCHAR(100),
  peso_entrega DECIMAL(5,2),
  FOREIGN KEY (id_veiculo) REFERENCES Veiculos(id_veiculo)
);
```

### Inserção de dados

```sql
INSERT INTO Entregas (id_entrega, id_veiculo, data_entrega, cidade, peso_entrega) VALUES
(1, 1, '2024-08-01', 'São Paulo', 8.0),
(2, 2, '2024-08-03', 'Rio de Janeiro', 12.0),
(3, 3, '2024-08-05', 'Brasília', 2.5),
(4, 1, '2024-08-07', 'Belo Horizonte', 9.5),
(5, 4, '2024-08-10', 'Salvador', 11.0),
(6, 2, '2024-08-12', 'São Paulo', 13.5),
(7, 3, '2024-08-14', 'Brasília', 2.0);
```

***

## 5️⃣ TAREFA 1

### Filtrar entregas feitas em São Paulo após 2024‑08‑01 (WHERE)

### Consulta SQL

```sql
SELECT *
FROM Entregas
WHERE cidade = 'São Paulo'
  AND data_entrega > '2024-08-01';
```

### ✅ Explicação detalhada

*   `WHERE` filtra linhas antes de qualquer agrupamento;
*   condição 1: cidade igual a São Paulo;
*   condição 2: data posterior a 01/08/2024;
*   retorna apenas registros que atendem **ambas** as condições.

### 📌 Resultado esperado

| id\_entrega | id\_veiculo | data\_entrega | cidade    | peso\_entrega |
| ----------- | ----------- | ------------- | --------- | ------------- |
| 6           | 2           | 2024-08-12    | São Paulo | 13.5          |

***

## 6️⃣ TAREFA 2

### Filtrar veículos com capacidade maior que 10 toneladas (WHERE)

### Consulta SQL

```sql
SELECT *
FROM Veiculos
WHERE capacidade_carga > 10.0;
```

### ✅ Explicação

*   `WHERE` filtra diretamente os dados da tabela;
*   não há agregação;
*   busca veículos com maior capacidade de carga.

### 📌 Resultado esperado

| id\_veiculo | modelo     | ano  | capacidade\_carga |
| ----------- | ---------- | ---- | ----------------- |
| 2           | Caminhão B | 2020 | 15.0              |
| 4           | Caminhão C | 2022 | 12.0              |

***

## 7️⃣ TAREFA 3

### Agrupar entregas por cidade com peso total acima de 15 toneladas (HAVING)

### Consulta SQL

```sql
SELECT cidade,
       SUM(peso_entrega) AS total_peso
FROM Entregas
GROUP BY cidade
HAVING SUM(peso_entrega) > 15.0;
```

### ✅ Explicação passo a passo

*   `GROUP BY cidade` agrupa as entregas por cidade;
*   `SUM(peso_entrega)` calcula o peso total por cidade;
*   `HAVING` filtra **após a agregação**;
*   somente cidades com peso total acima de 15 toneladas aparecem.

### 📌 Resultado esperado

| cidade    | total\_peso |
| --------- | ----------- |
| São Paulo | 21.5        |

***

## 8️⃣ TAREFA 4

### Identificar veículos com entregas acima de 10 toneladas no total (JOIN + HAVING)

### Consulta SQL

```sql
SELECT v.modelo,
       SUM(e.peso_entrega) AS total_peso_entregas
FROM Veiculos v
INNER JOIN Entregas e
ON v.id_veiculo = e.id_veiculo
GROUP BY v.modelo
HAVING SUM(e.peso_entrega) > 10.0;
```

### ✅ Explicação detalhada

*   `JOIN` relaciona veículos e entregas;
*   agrupamento por modelo do veículo;
*   cálculo do peso total entregue por veículo;
*   `HAVING` filtra apenas os veículos com total acima de 10 toneladas.

### 📌 Resultado esperado

| modelo     | total\_peso\_entregas |
| ---------- | --------------------- |
| Caminhão A | 17.5                  |
| Caminhão B | 25.5                  |
| Caminhão C | 11.0                  |

***

## 9️⃣ Conceitos Trabalhados na Atividade

✅ WHERE – filtragem de registros  
✅ GROUP BY – agrupamento de dados  
✅ HAVING – filtragem de grupos  
✅ JOIN – relacionamento entre tabelas  
✅ SUM – função de agregação  
✅ Análise logística e operacional

***

## 🔟 Forma de Entrega da Atividade

📌 **Entrega recomendada:**

*   Documento **PDF ou DOCX** contendo:
    *   descrição do problema;
    *   criação das tabelas;
    *   inserção dos dados;
    *   todas as consultas SQL;
    *   explicação de cada tarefa;
*   Nome e turma preenchidos;
*   Código SQL organizado e comentado.

***

## 1️⃣1️⃣ Conclusão

Esta atividade demonstrou como o SQL é essencial para análise de dados operacionais em empresas de logística. A combinação de filtros, agrupamentos e junções permite extrair informações estratégicas, como desempenho por cidade e eficiência dos veículos.

