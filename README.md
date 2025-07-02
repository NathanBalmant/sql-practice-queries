
# 📘 Projeto: Exercícios SQL com Base de Dados Acadêmica

Este repositório contém a resolução de diversas questões para aprender SQL. As consultas abordam operações como `JOIN`, `AGGREGATE FUNCTIONS`, `GROUP BY`, `UPDATE` e manipulação de datas, simulando cenários comuns de um sistema de biblioteca e avaliação escolar.

---

## 📌 Enunciados e Respostas

### 📄 Questão 1
**Enunciado:**  
Liste a quantidade de exemplares disponíveis para cada livro no acervo, mostrando também o título e o ISBN do livro.

**Resposta:**
```sql
SELECT COUNT(EXEMPLAR.CODLIVRO),
       LIVRO.TITULO,
       LIVRO.ISBN
FROM exemplar
JOIN LIVRO ON EXEMPLAR.CODLIVRO = LIVRO.CODLIVRO
WHERE EXEMPLAR.SITUACAO = "disponível"
GROUP BY EXEMPLAR.CODLIVRO;
```

---

### 📄 Questão 2
**Enunciado:**  
Liste os dados (nome, CPF e data de nascimento) das pessoas que são ao mesmo tempo professores e alunos.

**Resposta:**
```sql
SELECT 
    Pessoa.nome,
    Pessoa.cpf,
    Pessoa.datanascimento
FROM PESSOA
JOIN ALUNO ON Aluno.codPessoa = pessoa.codPessoa
JOIN Professor ON Professor.codPessoa = pessoa.codPessoa;
```

---

### 📄 Questão 3
**Enunciado:**  
Calcule a média das notas de cada aluno, considerando apenas as provas em que o aluno teve nota superior a 60% do valor total da prova.

**Resposta:**
```sql
SELECT avg(PROVAALUNO.NOTA) AS MEDIANOTAS,
       ALUNO.MATRICULA
FROM PROVAALUNO
JOIN PROVA ON PROVA.CODPROVA = PROVAALUNO.CODPROVA
JOIN ALUNO ON PROVA.CODPESSOA = ALUNO.CODPESSOA
WHERE PROVAALUNO.NOTA > (PROVA.VALOR * 60) / 100 
GROUP BY PROVAALUNO.CODPESSOA;
```

---

### 📄 Questão 4
**Enunciado:**  
Para cada prova aplicada a partir de julho, mostre a média das notas obtidas pelos alunos, a quantidade de alunos que realizaram a prova, o nome do professor responsável, a descrição da prova, a data e o valor.

**Resposta:**
```sql
SELECT avg(PROVAALUNO.NOTA) AS MEDIAS,
       count(PROVAALUNO.codpessoa) as qtedAlunos,
       PESSOA.NOME,
       PROVA.DESCRICAO,
       PROVA.DATAAPLICACAO,
       PROVA.VALOR
FROM PROVAALUNO
JOIN PROVA ON PROVA.CODPROVA = PROVAALUNO.CODPROVA
JOIN PROFESSOR ON PROVA.CODPESSOA = PROFESSOR.CODPESSOA
JOIN PESSOA ON PESSOA.CODPESSOA = PROFESSOR.CODPESSOA
WHERE month(PROVA.DATAAPLICACAO) > 6
GROUP BY PROVAALUNO.CODPROVA;
```

---

### 📄 Questão 5
**Enunciado:**  
Adicione uma nova coluna chamada `quantidade` na tabela `EMPRESTIMO` e atualize seu valor com a diferença entre o dia da devolução e o dia do prazo de devolução, para os empréstimos já devolvidos.

**Resposta:**
```sql
ALTER TABLE EMPRESTIMO ADD COLUMN QUANTIDADE NUMERIC(9,2);

UPDATE EMPRESTIMO
SET QUANTIDADE = day(EMPRESTIMO.DATADEVOLUCAO) - day(EMPRESTIMO.PRAZODEVOLUCAO)
WHERE EMPRESTIMO.DATADEVOLUCAO IS NOT NULL;
```

---



---

### 📄 Questão 7
**Enunciado:**  
Liste os dados de todos os empréstimos que foram devolvidos com atraso antes do mês de julho. Mostre a data de retirada, prazo de devolução, data de devolução, nome e CPF da pessoa, ISBN e título do livro.

**Resposta:**
```sql
SELECT 
    EMPRESTIMO.DATARETIRADA,
    EMPRESTIMO.DATADEVOLUCAO,
    EMPRESTIMO.PRAZODEVOLUCAO,
    PESSOA.NOME,
    PESSOA.CPF,
    LIVRO.ISBN,
    LIVRO.TITULO
FROM EMPRESTIMO
JOIN PESSOA ON PESSOA.CODPESSOA = EMPRESTIMO.CODPESSOA
JOIN EXEMPLAR ON EMPRESTIMO.CODEXEMPLAR = EXEMPLAR.CODEXEMPLAR
JOIN LIVRO ON LIVRO.CODLIVRO = EXEMPLAR.CODLIVRO
WHERE EMPRESTIMO.DATADEVOLUCAO IS NOT NULL AND
      EMPRESTIMO.DATADEVOLUCAO > EMPRESTIMO.PRAZODEVOLUCAO AND
      EMPRESTIMO.DATARETIRADA IS NOT NULL AND
      month(EMPRESTIMO.DATADEVOLUCAO) < 7;
```

---

### 📄 Questão 8
**Enunciado:**  
Liste os casos em que alunos tiveram nota inválida em uma prova (maior que o valor da prova ou negativa). Mostre a descrição da prova, valor, data de aplicação, nota do aluno, matrícula e nome do aluno.

**Resposta:**
```sql
SELECT PROVA.DESCRICAO AS PROVA,
       PROVA.VALOR,
       PROVA.DATAAPLICACAO,
       PROVAALUNO.NOTA,
       ALUNO.MATRICULA,
       PESSOA.NOME AS ALUNO
FROM PROVAALUNO
JOIN PROVA ON PROVA.CODPROVA = PROVAALUNO.CODPROVA
JOIN ALUNO ON PROVAALUNO.CODPESSOA = ALUNO.CODPESSOA
JOIN PESSOA ON PESSOA.CODPESSOA = ALUNO.CODPESSOA
WHERE PROVAALUNO.NOTA > PROVA.VALOR OR
      PROVAALUNO.NOTA < 0
ORDER BY PROVA.DESCRICAO;
```
