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
```
SELECT firstName, lastName, city, state
FROM Person p
LEFT JOIN Address a
on p.personId = a.personId;
```

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
```
SELECT IFNULL(
    (SELECT DISTINCT Salary
    FROM Employee
    ORDER BY Salary DESC
    LIMIT 1
    OFFSET 1), NULL) AS SecondHighestSalary
```
    
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
```
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
```

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
```
SELECT score, DENSE_RANK() OVER (ORDER BY Score DESC) AS 'Rank'
FROM Scores
```

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
```
SELECT a.NUM AS ConsecutiveNums
FROM Logs a, Logs b, Logs c
JOIN Logs b ON a.id = b.id + 1 AND a.Num = b.Num
JOIN Logs c ON a.id = c.id + 2 AND a.Num = c.Num
```

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
```
SELECT e1.name as Employee
FROM Employee e1
JOIN Employee e2 on e1.ManagerId = e2.id
WHERE e1.Salary > e2.Salary
```

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
```
SELECT Email
FROM (SELECT Email, COUNT(Email) AS c
      FROM Person
      GROUP BY Email) AS temp_table
WHERE c > 1
```

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
```
SELECT name AS Customers
FROM Customers
LEFT JOIN Orders ON Customers.id = Orders.CustomerId
WHERE CustomerId IS NULL
```

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
```
SELECT Department.name AS 'Department', Employee.name AS 'Employee', Salary
FROM Employee
JOIN Department ON Employee.DepartmentiD = Department.Id
WHERE (Employee.DepartmentId, Salary) IN (
            SELECT departmentId, MAX(salary)
            FROM Employee
            GROUP BY DepartmentId
)
```

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
```
SELECT Department.name AS 'Department', e.name AS 'Employee', e.Salary
FROM (SELECT DepartmentId, Name, Salary, DENSE_RANK() OVER (PARTITION BY DepartmentId ORDER BY Salary DESC) AS r
      FROM Employee) AS e
JOIN Department ON e.DepartmentId = Department.id
WHERE r <= 3
```

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
```
DELETE p2
FROM Person p1
JOIN Person p2 ON p1.Email = p2.Email
AND p1.id < p2.id
```

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
```
SELECT w2.id
FROM Weather w1
JOIN Weather w2 ON DATEDIFF(w1.recordDate, w2.recordDate) = -1
WHERE w2.temperature > w1.temperature
```

### Explicação
Junta-se duas colunas iguais, considerando que a data da tabela w1 deve ser o dia anterior da data da tabela w2.
Após isso, considera-se que a temperatura da segunda tabela (dia n) seja maior que a temperatura da primeira tabela (dia n-1) e seleciona-se o id da segunda tabela.



## 262. Trips and Users

Table: Trips
```
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| id          | int      |
| client_id   | int      |
| driver_id   | int      |
| city_id     | int      |
| status      | enum     |
| request_at  | date     |     
+-------------+----------+
```
id is the primary key for this table.
The table holds all taxi trips. Each trip has a unique id, while client_id and driver_id are foreign keys to the users_id at the Users table.
Status is an ENUM type of ('completed', 'cancelled_by_driver', 'cancelled_by_client').
 

Table: Users
```
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| users_id    | int      |
| banned      | enum     |
| role        | enum     |
+-------------+----------+
```
users_id is the primary key for this table.
The table holds all users. Each user has a unique users_id, and role is an ENUM type of ('client', 'driver', 'partner').
banned is an ENUM type of ('Yes', 'No').
 

The cancellation rate is computed by dividing the number of canceled (by client or driver) requests with unbanned users by the total number of requests with unbanned users on that day.

Write a SQL query to find the cancellation rate of requests with unbanned users (both client and driver must not be banned) each day between "2013-10-01" and "2013-10-03". Round Cancellation Rate to two decimal points.

Return the result table in any order.

### Resolução
```
SELECT Request_at AS Day, ROUND(SUM(IF(Status != 'completed',1,0))/COUNT(Status),2) AS 'Cancellation Rate'
FROM Trips
WHERE Request_at >= '2013-10-01' AND Request_at <= '2013-10-03'
    AND Client_id NOT IN (SELECT Users_id FROM Users WHERE Banned = 'Yes')
    AND Driver_id NOT IN (SELECT Users_id FROM Users WHERE Banned = 'Yes')
GROUP BY Request_at
```

### Explicação
Problemas:
 1 - taxa de cancelamento - Feita dividindo o número de cancelamentos com o número total de corridas - Usa-se ROUND() para usar duas casas decimais
 2 - datas - Usa-se WHERE e coloca duas condições
 3 - Usuários não banidos - Faz subqueries para escolher apenas o usuário e o motorista não banidos, com isso condiciona-se na query principal, usando WHERE/AND

## 511. Game Play Analysis I

Table: Activity
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
```
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some games.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on someday using some device.
 

Write an SQL query to report the first login date for each player.

Return the result table in any order.

### Resolução
```
SELECT player_id, MIN(event_date) AS first_login
FROM Activity
GROUP BY player_id
```

### Explicação
Problema: 
 1 - Esolha de apenas o primeiro login - Usa-se a função MIN() e agrupa os dados de acordo com o player_id, logo cada player_id só mostrará o primeiro login

## 512. Game Play Analysis II

Table: Activity
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
```
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some game.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on some day using some device.
Write a SQL query that reports the device that is first logged in for each player.

### Resolução
```
SELECT player_id, device_id
FROM (SELECT player_id, device_id, ROW_NUMBER() OVER (PARTITION BY player_id ORDER BY event_date) AS r
      FROM Activity) AS subq
WHERE r = 1
```

### Explicação
Primeiro, cria-se uma subquery, a qual particiona o player_id, ordenando pela event_data, com isso, sabe-se, ao usar ROW_NUMBER(), a primeira vez que cada pessoa usou o computador.
Logo, ao usar uma condição WHERE r = 1, na query principal, decobre-se o primeiro dispositivo que cada pessoa usou.



## 534. Game Play Analysis III

Table: Activity
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
```
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some game.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on some day using some device.
 

Write an SQL query that reports for each player and date, how many games played so far by the player. That is, the total number of games played by the player until that date. 

### Resolução
```
SELECT a.player_id, a.event_date, SUM(b.games_played)
FROM Activity a
JOIN Activity b ON a.player_id = b.player_id AND a.event_date >= b.event_date
GROUP BY a.player_id, a.event_date
```

### Explicação
Cria-se um SELF JOIN, o qual considera os elementos de mesmo indicie e que a data da primeira tabela seja maior que a data da segunda tabela.
Com isso, seleciona-se a SUM() dos jogos jogados na segunda tabela.



## 550. Game Play Analysis IV

Table: Activity
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
```
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some game.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on some day using some device.
 

Write an SQL query that reports the fraction of players that logged in again on the day after the day they first logged in, rounded to 2 decimal places. In other words, you need to count the number of players that logged in for at least two consecutive days starting from their first login date, then divide that number by the total number of players.

### Resolução
```
SELECT ROUND(COUNT(DISTINCT b.player_id)/COUNT(DISINCT a.player_id),2) AS Fraction
FROM (SELECT player_id, MIN(event_date) AS event_date
      FROM Activity
      GROUP BY player_id) AS a
LEFT JOIN Actibity b ON a.player_id = b.player_id AND a.event_date + 1 = b.event_date
```

### Explicação
Primeiro, cria-se uma subquery, selecionando o primeiro login e agrupa-a considerando o player_id.
Após isso, usa-se um LEFT JOIN, considerando que o player_id da primeira tabela seja igual ao player_id da segunda tabela e que a adição da data de login da primeira tabela (que pela subquery, é o primeiro login) com 1 seja a data da segunda tabela. Desse modo, há a escolha de apenas os logins sucessivos, considerando o primeiro como o primeiro login feito.
Assim, é selecionado o número de jogadores distintos, que logaram no dia seguinte de seu primeiro login, dividido pelo número total de jogadores. E, alem disso, Arredonda-se, em duas casas decimais, tal fração.



## 570. Managers with at Least 5 Direct Reports

The Employee table holds all employees including their managers. Every employee has an Id, and there is also a column for the manager Id.
```
+------+----------+-----------+----------+
|Id    |Name 	  |Department |ManagerId |
+------+----------+-----------+----------+
|101   |John 	  |A 	      |null      |
|102   |Dan 	  |A 	      |101       |
|103   |James 	  |A 	      |101       |
|104   |Amy 	  |A 	      |101       |
|105   |Anne 	  |A 	      |101       |
|106   |Ron 	  |B 	      |101       |
+------+----------+-----------+----------+
```
Given the Employee table, write a SQL query that finds out managers with at least 5 direct report. For the above table, your SQL query should return:
```
+-------+
| Name  |
+-------+
| John  |
+-------+
```
Note:
No one would report to himself.

### Resolução
```
SELECT Name
FROM Employee
WHERE Id IN (SELECT ManagerId FROM Employee
            GROUP BY ManagerId
            HAVING (COUNT(DISTINCT Id)) >= 5)
```

### Explicação
Cria-se uma subquery, a qual seleciona apenas o ManagerId referenciado por 5 Id diferentes.
Após isso, seleciona o nome de tal Id referenciado.
