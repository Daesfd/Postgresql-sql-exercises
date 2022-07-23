# Conexão ao Postgresql usando docker e resolução de exercícios.

## 175. Combine Two Tables
Table: Person
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| personId    | int     |
| lastName    | varchar |
| firstName   | varchar |
+-------------+---------+
```
personId is the primary key column for this table.
This table contains information about the ID of some persons and their first and last names.
 

Table: Address
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| addressId   | int     |
| personId    | int     |
| city        | varchar |
| state       | varchar |
+-------------+---------+
```
addressId is the primary key column for this table.
Each row of this table contains information about the city and state of one person with ID = PersonId.
 

Write an SQL query to report the first name, last name, city, and state of each person in the Person table. If the address of a personId is not present in the Address table, report null instead.

### Resolução
SELECT firstName, lastName, city, state
FROM Person p
LEFT JOIN Address a
on p.personId = a.personId;

### Explicação
Uso do left join para combinar as duas tabelas, considerando o id da pessoa como a conexão entre as duas tabelas e, com isso, a seleção das colunas pedidas.



## 176. Second Highest Salary

able: Employee
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
```
id is the primary key column for this table.
Each row of this table contains information about the salary of an employee.
 

Write an SQL query to report the second highest salary from the Employee table. If there is no second highest salary, the query should report null.

### Resolução
SELECT IFNULL(
    (SELECT DISTINCT Salary
    FROM Employee
    ORDER BY Salary DESC
    LIMIT 1
    OFFSET 1), NULL) AS SecondHighestSalary
    
### Explicação
Seleção de salários distintos, ordenados de modo decrescente e exclusão do maior salário com o uso de offset.
Limitando para um único salário, há a seleção do segundo maior salário e, caso não exista-o, retorna um valor nulo



## 177. Nth Highest Salary

Table: Employee
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
```
id is the primary key column for this table.
Each row of this table contains information about the salary of an employee.
 

Write an SQL query to report the nth highest salary from the Employee table. If there is no nth highest salary, the query should report null.

### Resolução
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
     SELECT DISTINCT Salary
     FROM Employee
     ORDER BY Salary DESC
     LIMIT 1
     OFFSET N - 1
  );
END

### Explicação
Criação de uma função, a qual recebe um número inteiro e retorna um npumero inteiro, o qual é n-ésimo maior salário.
Nesta função há a seleção do salaŕio distinto e a ordenação de modo decrescente do salário.
Após isso, há a limitação para apenas um salário aparecer e a exclusão dos n-1-ésimos salários.



## 178. Rank Scores

Table: Scores
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| score       | decimal |
+-------------+---------+
```
id is the primary key for this table.
Each row of this table contains the score of a game. Score is a floating point value with two decimal places.
 

Write an SQL query to rank the scores. The ranking should be calculated according to the following rules:

The scores should be ranked from the highest to the lowest.
If there is a tie between two scores, both should have the same ranking.
After a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no holes between ranks.
Return the result table ordered by score in descending order.

### Resolução
SELECT score, DENSE_RANK() OVER (ORDER BY Score DESC) AS 'Rank'
FROM Scores

### Explicação
Como é necessário rankear do maior para o menor, usa-se ORDER BY desc.
Como é necessário mostrar todos os elementos do n-ésimo rank e o próximo rank ser o n+1-ésimo rank, usa-se DENSE_RANK

Seleciona uma coluna, baseando-se nas notas, de modo decrescente, usando DENSE_RANK



## 180. Consecutive Numbers

Table: Logs
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
```
id is the primary key for this table.
id is an autoincrement column.
 

Write an SQL query to find all numbers that appear at least three times consecutively.

Return the result table in any order.

### Resolução
SELECT a.NUM AS ConsecutiveNums
FROM Logs a, Logs b, Logs c
JOIN Logs b ON a.id = b.id + 1 AND a.Num = b.Num
JOIN Logs c ON a.id = c.id + 2 AND a.Num = c.Num
    
### Explicação
Usando três tabelas iguais, compara-se, usando JOIN, se o número da tabela 'a' é igual ao número da tabela 'b', considerando que o id desse número da tabela 'b' seja o id da tabela 'a' + 1.
Após isso, faz-se o mesmo com a terceira tabela, mas considera-se que o id da tabela 'c' seja o id da tabela 'a' + 2.



## 181. Employees Earning More Than Their Managers

Table: Employee
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| salary      | int     |
| managerId   | int     |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table indicates the ID of an employee, their name, salary, and the ID of their manager.
 

Write an SQL query to find the employees who earn more than their managers.

Return the result table in any order.

### Resolução
SELECT e1.name as Employee
FROM Employee e1
JOIN Employee e2 on e1.ManagerId = e2.id
WHERE e1.Salary > e2.Salary

### Explicação
Junta-se duas tabelas, considerando que o ManagerId da primeira tabela seja igual ao id da segunda tabela, indicando, assim, a conexão entre o trabalhador, o qual tem um ManagerId, e seu superior, o qual tem seu id ligado pelo ManagerId do trabalhador.
Após isso, coloca-se uma condição, a qual explicita que o trabalhador tenha receber mais que seu superior.



## 182. Duplicate Emails

Table: Person
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table contains an email. The emails will not contain uppercase letters.
 

Write an SQL query to report all the duplicate emails.

Return the result table in any order.

### Resolução
SELECT Email
FROM (SELECT Email, COUNT(Email) AS c
      FROM Person
      GROUP BY Email) AS temp_table
WHERE c > 1

### Explicação
Faz-se uma subquery, criando uma coluna, que tem o número de vezes que um email aparece (COUNT(EMAIL)).
Após isso, faz uma query, em relação a esta subquery, que considera que a coluna COUNT(Email) seja maior que 1.



## 183. Customers Who Never Order

Table: Customers
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table indicates the ID and name of a customer.
 

Table: Orders
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| customerId  | int  |
+-------------+------+
```
id is the primary key column for this table.
customerId is a foreign key of the ID from the Customers table.
Each row of this table indicates the ID of an order and the ID of the customer who ordered it.
 

Write an SQL query to report all customers who never order anything.

Return the result table in any order.

### Resolução
SELECT name AS Customers
FROM Customers
LEFT JOIN Orders ON Customers.id = Orders.CustomerId
WHERE CustomerId IS NULL

### Explicação
Junta-se as tabelas, considerando que o id da tabela Customer seja igual o CustomerId da tabela Orders.
Após isso, seleciona o nome do cliente, que tenha o CustomerId nulo



## 184. Department Highest Salary

Table: Employee
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |
+--------------+---------+
```
id is the primary key column for this table.
departmentId is a foreign key of the ID from the Department table.
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.
 

Table: Department
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table indicates the ID of a department and its name.
 

Write an SQL query to find employees who have the highest salary in each of the departments.

Return the result table in any order.

### Resolução
SELECT Department.name AS 'Department', Employee.name AS 'Employee', Salary
FROM Employee
JOIN Department ON Employee.DepartmentiD = Department.Id
WHERE (Employee.DepartmentId, Salary) IN (
            SELECT departmentId, MAX(salary)
            FROM Employee
            GROUP BY DepartmentId
)

### Explicação
Primeiro, cria-se um subquery para selecionar o salário máximo de cada departamento.
Após isso, junta-se as duas tabelas, considerando que o Employee.DepartmentId seja igual ao Deparment.Id, e seleciona as colunas pedidas.



## 185. Department Top Three Salaries

Table: Employee
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |
+--------------+---------+
```
id is the primary key column for this table.
departmentId is a foreign key of the ID from the Department table.
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.
 

Table: Department
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table indicates the ID of a department and its name.
 

A company's executives are interested in seeing who earns the most money in each of the company's departments. A high earner in a department is an employee who has a salary in the top three unique salaries for that department.

Write an SQL query to find the employees who are high earners in each of the departments.

Return the result table in any order.

### Resolução
SELECT Department.name AS 'Department', e.name AS 'Employee', e.Salary
FROM (SELECT DepartmentId, Name, Salary, DENSE_RANK() OVER (PARTITION BY DepartmentId ORDER BY Salary DESC) AS r
      FROM Employee) AS e
JOIN Department ON e.DepartmentId = Department.id
WHERE r <= 3

### Explicação
Cria-se uma subquery, a qual cria uma tabela (e) com uma coluna (r) de DENSE_RANK em partições de DepartmentId e ordana-a em relação a salários decrescentes.
Após isso, junta-se esta nova tabela com a tabela Deparment, considerando que e.DeparmentId seja igual a Depatment.id e coloca a condição de que a coluna r seja menor ou igual a 3, ou seja, selecionando os três maiores salários



## 196. Delete Duplicate Emails

Table: Person
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table contains an email. The emails will not contain uppercase letters.
 

Write an SQL query to delete all the duplicate emails, keeping only one unique email with the smallest id. Note that you are supposed to write a DELETE statement and not a SELECT one.

After running your script, the answer shown is the Person table. The driver will first compile and run your piece of code and then show the Person table. The final order of the Person table does not matter.

### Resolução
DELETE p2
FROM Person p1
JOIN Person p2 ON p1.Email = p2.Email
AND p1.id < p2.id

### Explicação
Junta-se duas tabelas iguais, igualando-se os emails das duas tabelas e coloca a condição de que o id da segunda tabela seja maior que o id da primeira tabela. Com isso, desse modo, pega-se os emails repitidos.
Após isso, deleta a segunda tabela, a qual tem os emails repetidos.



## 197. Rising Temperature

Table: Weather
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
```
id is the primary key for this table.
This table contains information about the temperature on a certain day.
 

Write an SQL query to find all dates' Id with higher temperatures compared to its previous dates (yesterday).

Return the result table in any order.

### Resolução
SELECT w2.id
FROM Weather w1
JOIN Weather w2 ON DATEDIFF(w1.recordDate, w2.recordDate) = -1
WHERE w2.temperature > w1.temperature

### Explicação
Junta-se duas colunas iguais, considerando que a data da tabela w1 deve ser o dia anterior da data da tabela w2.
Após isso, considera-se que a temperatura da segunda tabela (dia n) seja maior que a temperatura da primeira tabela (dia n-1) e seleciona-se o id da segunda tabela.
