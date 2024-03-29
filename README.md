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



## 584. Find Customer Referee

Table: Customer
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| referee_id  | int     |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table indicates the id of a customer, their name, and the id of the customer who referred them.
 

Write an SQL query to report the names of the customer that are not referred by the customer with id = 2.

Return the result table in any order.

### Resolução
```
SELECT name
FROM Customer
WHERE referee_id != 2 OR referee_id IS NULL
```

### Explicação
Usa-se uma condição, a qual não escolhe caso o id seja referido pelo id = 2 ou seja nulo.



## 586. Customer Placing the Largest Number of Orders

Table: Orders
```
+-----------------+----------+
| Column Name     | Type     |
+-----------------+----------+
| order_number    | int      |
| customer_number | int      |
+-----------------+----------+
```
order_number is the primary key for this table.
This table contains information about the order ID and the customer ID.
 

Write an SQL query to find the customer_number for the customer who has placed the largest number of orders.

The test cases are generated so that exactly one customer will have placed more orders than any other customer.

### Resolução
```
SELECT customer_number
FROM Orders
GROUP BY customer_number
ORDER BY COUNT(*) DESC
LIMIT 1
```

### Explicação
Cada ordem é pedida por um cliente, logo, a query é agrupada pelo customer_number e ordenada, de modo decrescente. pelo número de pedidos por clientes.
Após isso, é limitada em apenas 1 cliente, que é o cliente que mais pediu.



## 595. Big Countries

Table: World
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | int     |
+-------------+---------+
```
name is the primary key column for this table.
Each row of this table gives information about the name of a country, the continent to which it belongs, its area, the population, and its GDP value.
 

A country is big if:

it has an area of at least three million (i.e., 3000000 km2), or
it has a population of at least twenty-five million (i.e., 25000000).
Write an SQL query to report the name, population, and area of the big countries.

Return the result table in any order.

### Resolução
```
SELECT name, population, area
FROM WORLD
WHERE area >= 3000000
UNION
SELECT name, population, area
FROM WORLD
WHERE population >= 25000000
```

### Explicação
Cria-se duas condições, as quais são unidas por UNION. A primeira delas é a exigência de uma area >= 3000000 km2, a segunda delas é a exigência de uma população >= 25000000.



## 596. Classes More Than 5 Students

Table: Courses
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| student     | varchar |
| class       | varchar |
+-------------+---------+
```
(student, class) is the primary key column for this table.
Each row of this table indicates the name of a student and the class in which they are enrolled.
 

Write an SQL query to report all the classes that have at least five students.

Return the result table in any order.

### Resolução
```
SELECT class
FROM (
    SELECT class, COUNT(DISTINCT student) AS num
    FROM courses
    GROUP BY class) AS temp_table
WHERE num >= 5
```

### Explicação
Cria-se uma subquery, a qual cria uma coluna que conta os números de diferentes estudantes e agrupa-os em diferentes classes.
Após isso, na query principal, escolhe-se a classe com um numéro de estudantes maior ou igual a 5.



## 601. Human Traffic of Stadium

Table: Stadium
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| visit_date    | date    |
| people        | int     |
+---------------+---------+
```
visit_date is the primary key for this table.
Each row of this table contains the visit date and visit id to the stadium with the number of people during the visit.
No two rows will have the same visit_date, and as the id increases, the dates increase as well.
 

Write an SQL query to display the records with three or more rows with consecutive id's, and the number of people is greater than or equal to 100 for each.

Return the result table ordered by visit_date in ascending order.

### Resolução
```
SELECT DISTINCT t1.*
FROM stadium t1, stadium t2, stadium t3
WHERE (t1.people >= 100 AND
       t2.people >= 100 AND
       t3.people >= 100 AND (
       (t1.id - t2.id = 1 AND t1.id - t3.id = 2 AND t2.id - t3.id = 1) OR
       (t2.id - t1.id = 1 AND t2.id - t3.id = 2 AND t1.id - t3.id = 1) OR
       (t3.id - t2.id = 1 AND t2.id - t1.id = 1 AND t3.id - t1.id = 2)))
ORDER BY t1.id
```

### Explicação
A partir de três tabelas iguais, condiciona-as de modo que:
  - Todas as pessoas das tabelas devem ser maiores que 100
  - Seja necessário a consecutividade dentre os id, independente da ordem.



## 607. Sales Person

Table: SalesPerson
```
+-----------------+---------+
| Column Name     | Type    |
+-----------------+---------+
| sales_id        | int     |
| name            | varchar |
| salary          | int     |
| commission_rate | int     |
| hire_date       | date    |
+-----------------+---------+
```
sales_id is the primary key column for this table.
Each row of this table indicates the name and the ID of a salesperson alongside their salary, commission rate, and hire date.
 

Table: Company
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| com_id      | int     |
| name        | varchar |
| city        | varchar |
+-------------+---------+
```
com_id is the primary key column for this table.
Each row of this table indicates the name and the ID of a company and the city in which the company is located.
 

Table: Orders
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| order_id    | int  |
| order_date  | date |
| com_id      | int  |
| sales_id    | int  |
| amount      | int  |
+-------------+------+
```
order_id is the primary key column for this table.
com_id is a foreign key to com_id from the Company table.
sales_id is a foreign key to sales_id from the SalesPerson table.
Each row of this table contains information about one order. This includes the ID of the company, the ID of the salesperson, the date of the order, and the amount paid.
 

Write an SQL query to report the names of all the salespersons who did not have any orders related to the company with the name "RED".

Return the result table in any order.

### Resolução
```
SELECT s.name
FROM salesperson s
WHERE s.sales_id NOT IN (
    SELECT o.sales_id
    FROM orders o
    LEFT JOIN company c ON o.com_id = c.com_id
    WHERE c.name = 'RED')
```

### Explicação
Primeiro, cria-se uma subquery, a qual seleciona o sales_id, da tabela orders, e junta-o, em um LEFT JOIN, com a tabela company, considernado que o com_id de ambas tabelas sejam iguais. Após isso, coloca-se a exigência do nome da companhia ser 'RED'.
Desse modo, na query principal, seleciona-se o nome do vendedor, da tabela salesperson, que não está na subquery



## 608. Tree Node

Table: Tree
```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| p_id        | int  |
+-------------+------+
```
id is the primary key column for this table.
Each row of this table contains information about the id of a node and the id of its parent node in a tree.
The given structure is always a valid tree.
 

Each node in the tree can be one of three types:

"Leaf": if the node is a leaf node.
"Root": if the node is the root of the tree.
"Inner": If the node is neither a leaf node nor a root node.
Write an SQL query to report the type of each node in the tree.

Return the result table ordered by id in ascending order.

### Resolução
```
SELECT id, 'Root' AS Type
FROM tree
WHERE p_id IS NULL

UNION

SELECT id, 'Leaf' AS Type
FROM tree
WHERE id NOT IN (
    SELECT DISTINCT p_id
    FROM tree
    WHERE p_id IS NOT NULL) AND p_id IS NOT NULL
    
UNION

SELECT id, 'Inner' AS Type
FROM tree
WHERE id IN (
    SELECT DISTINCT p_id
    FROM tree
    WHERE p_id IS NOT NULL) AND p_id IS NOT NULL
GROUP BY id
```

### Explicação
O objetivo é criar os tipos de nós, baseando-se no número de nós pais, de modo que:
  - Caso o p_id seja nulo, é caracterizado como Root (ou seja, não tem pai)
  - Caso o p_id seja existente (tenha um pai) e o id não seja referenciado (não tem filho), é caracterizado como Leaf
  - Caso o p_id seja existente (tenha um pai) e o id seja referenciado (tem filho), é caracterizado como Inner
Desse modo, há a união das três situações e o agrupamento pelo id.



## 620. Not Boring Movies

Table: Cinema
```
+----------------+----------+
| Column Name    | Type     |
+----------------+----------+
| id             | int      |
| movie          | varchar  |
| description    | varchar  |
| rating         | float    |
+----------------+----------+
```
id is the primary key for this table.
Each row contains information about the name of a movie, its genre, and its rating.
rating is a 2 decimal places float in the range [0, 10]
 

Write an SQL query to report the movies with an odd-numbered ID and a description that is not "boring".

Return the result table ordered by rating in descending order.

### Resolução
```
SELECT *
FROM Cinema
WHERE MOD(id, 2) = 1 AND description != 'boring'
ORDER BY rating DESC
```

### Explicação
Coloca-se a exigência do id ser ímpar (MOD(id,2) = 1), e a descrição ser diferente de 'boring'.
Após isso, ordena os elementos de modo que o rating seja decrescente.



## 626. Exchange Seats

Table: Seat
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
```
id is the primary key column for this table.
Each row of this table indicates the name and the ID of a student.
id is a continuous increment.
 

Write an SQL query to swap the seat id of every two consecutive students. If the number of students is odd, the id of the last student is not swapped.

Return the result table ordered by id in ascending order.

### Resolução
```
SELECT (CASE
            WHEN MOD(id, 2) != 0 AND counts != id THEN id + 1
            WHEN MOD(id, 2) != 0 AND counts = id THEN id
            ELSE id - 1
        END) AS id, student
FROM seat, (SELECT COUNT(*) AS counts
            FROM seat) AS seat_counts
ORDER BY id ASC
```

### Explicação
Cria-se uma subquery que conta o número de assentos.
Após isso, na query principal, caso o id não ser par e o número de assentos ser diferente do id, então é adicionado 1 ao id. Caso o id não ser par e o número de assento ser igual ao id, então o id é mantido. Por fim, caso o id seja par, então é subraído 1 do id. (Em outras palavras, o id 1 vira id 2, o id 2 vira id 1 e, se o número de assentos ser ímpar, o último id continua o mesmo, caso contrário, o penúltimo id (ímpar) vira o último e o último id (par) vira o penúltimo.
Após isso, é ordenado, de modo ascendente, o id.


## 627. Swap Salary

Table: Salary
```
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| id          | int      |
| name        | varchar  |
| sex         | ENUM     |
| salary      | int      |
+-------------+----------+
```
id is the primary key for this table.
The sex column is ENUM value of type ('m', 'f').
The table contains information about an employee.
 

Write an SQL query to swap all 'f' and 'm' values (i.e., change all 'f' values to 'm' and vice versa) with a single update statement and no intermediate temporary tables.

Note that you must write a single update statement, do not write any select statement for this problem.


### Resolução
```
UPDATE salary
SET
    sex = IF (sex = 'm', 'f', 'm')
```

### Explicação
Atualiza a tabela, usando IF, caso o sexo seja 'm', é trocado por 'f', caso não seja, é trocado por 'm'.



## 1050. Actors and Directors Who Cooperated At Least Three Times

Table: ActorDirector
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| actor_id    | int     |
| director_id | int     |
| timestamp   | int     |
+-------------+---------+
```
timestamp is the primary key column for this table.
 

Write a SQL query for a report that provides the pairs (actor_id, director_id) where the actor has cooperated with the director at least three times.

Return the result table in any order.


### Resolução
```
SELECT actor_id, director_id
FROM ActorDirector
GROUP BY actor_id, director_id
HAVING COUNT(timestamp) >= 3
```

### Explicação
Agrupa-se a tabela de acordo com o actor_id e director_id, com isso, caso haja, para esse agrupamento, um número de timestamp maior ou igual a 3, é selecionado pela query.



## 1084. Sales Analysis III

Table: Product
```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |
+--------------+---------+
```
product_id is the primary key of this table.
Each row of this table indicates the name and the price of each product.
Table: Sales
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |
+-------------+---------+
```
This table has no primary key, it can have repeated rows.
product_id is a foreign key to the Product table.
Each row of this table contains some information about one sale.
 

Write an SQL query that reports the products that were only sold in the first quarter of 2019. That is, between 2019-01-01 and 2019-03-31 inclusive.

Return the result table in any order.

### Resolução
```
SELECT product_id, product_name
FROM Product
WHERE product_id IN (
    SELECT DISTINCT product_id
    FROM Sales
    WHERE sale_date BETWEEN '2019-01-01' AND '2019-03-31'
    )  AND product_id NOT IN (
        SELECT DISTINCT product_id
        FROM Sales
        WHERE sale_date < '2019-01-01' OR sale_date > '2019-03-31'
)
```

### Explicação
Primeiro, cria-se uma primeira subquery, que seleciona distintos product_id que tem sua data de venda antes de 2019-01-01 e depois de 2019-03-01.
Após isso, cria-se uma segunda subquery, que seleciona distintos product_id que tem sua data de venda entre 2019-01-01 e 2019-03-31.
Por fim, seleciona-se apenas os product_id que estão na segunda subquery e não estão na primeira subquery.



## 1141. User Activity for the Past 30 Days I

Table: Activity
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |
+---------------+---------+
```
There is no primary key for this table, it may have duplicate rows.
The activity_type column is an ENUM of type ('open_session', 'end_session', 'scroll_down', 'send_message').
The table shows the user activities for a social media website. 
Note that each session belongs to exactly one user.
 

Write an SQL query to find the daily active user count for a period of 30 days ending 2019-07-27 inclusively. A user was active on someday if they made at least one activity on that day.

Return the result table in any order.


### Resolução
```
SELECT activity_date AS 'day', COUNT(DISTINCT user_id) AS 'active_users'
FROM Activity
WHERE activity_date BETWEEN '2019-06-28' AND '2019-07-27'
GROUP BY day
```

### Explicação
Seleciona-se a activity_date como day e a contagem de distintos user_id como active_users, com a condição que a activity_date esteja entre 2019-06-28 e 2019-07-27 e agrupa-se por day.



## 1148. Article Views I

Table: Views
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| article_id    | int     |
| author_id     | int     |
| viewer_id     | int     |
| view_date     | date    |
+---------------+---------+
```
There is no primary key for this table, it may have duplicate rows.
Each row of this table indicates that some viewer viewed an article (written by some author) on some date. 
Note that equal author_id and viewer_id indicate the same person.
 

Write an SQL query to find all the authors that viewed at least one of their own articles.

Return the result table sorted by id in ascending order.


### Resolução
```
SELECT DISTINCT author_id AS 'id'
FROM Views
WHERE author_id = viewer_id
ORDER BY id

```

### Explicação
Seleciona-se distintos author_id como id, dado que o author_id do aritgo seja o mesmo que viewee_id do artigo.
Por fim, ordana-se por id.


## 1158. Market Analysis I
Table: Users
```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| join_date      | date    |
| favorite_brand | varchar |
+----------------+---------+
```
user_id is the primary key of this table.
This table has the info of the users of an online shopping website where users can sell and buy items.
 

Table: Orders
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| order_date    | date    |
| item_id       | int     |
| buyer_id      | int     |
| seller_id     | int     |
+---------------+---------+
```
order_id is the primary key of this table.
item_id is a foreign key to the Items table.
buyer_id and seller_id are foreign keys to the Users table.
 

Table: Items
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| item_id       | int     |
| item_brand    | varchar |
+---------------+---------+
```
item_id is the primary key of this table.
 

Write an SQL query to find for each user, the join date and the number of orders they made as a buyer in 2019.

Return the result table in any order.


### Resolução
```
SELECT user_id AS 'buyer_id', join_date, 0 AS 'orders_in_2019'
FROM Users
WHERE user_id NOT IN (
    SELECT buyer_id
    FROM Orders
    WHERE   order_date >= '2019-01-01' AND order_date <= '2019-12-31'
)

UNION

SELECT u.user_id AS 'buyer_id', u.join_date, COUNT(tb.order_id) AS 'orders_in_2019'
FROM (
    SELECT *
    FROM Orders
    WHERE order_date >= '2019-01-01' AND order_date <= '2019-12-31') tb
LEFT JOIN Users u
ON tb.buyer_id = u.user_id
GROUP BY u.user_id
```

### Explicação
1.
 a. Primeiro, cria-se uma subquery que seleciona apenas buyer_id que tenham sua order_date após 2019-01-01 e antes de 2019-12-31.
 b. Após isso, seleciona-se o user_id, como buyer_id, e join_date, como orders_in_2019, com a condição que o user_id não esteja na subquery.
 
2.
 a. Cria-se uma subquery, que seleciona apenas a order_date após 2019-01-01 e antes de 2019-12-31.
 b. Após isso, seleciona-se, de uma tabela principal (u), u.user_id, como buyer_id, u.join_date e, de uma tabela secundária (tb), a contagem de tb.order_id, como orders_in_2019.
 c. Depois, com o uso de left join, a partir da tabela principal (u), junta-se as duas tabelas, dado que tb.buyer_id seja igual a u.user_id.
 d. Por fim, agrupa-se por u.user_id

3. Uni-se 1. e 2.



## 1179. Reformat Department Table
Table: Department
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| revenue     | int     |
| month       | varchar |
+-------------+---------+
```
(id, month) is the primary key of this table.
The table has information about the revenue of each department per month.
The month has values in ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"].
 

Write an SQL query to reformat the table such that there is a department id column and a revenue column for each month.

Return the result table in any order.


### Resolução
```
SELECT id, SUM(CASE WHEN month = 'Jan' THEN revenue ELSE null END) AS 'Jan_Revenue',
    SUM(CASE WHEN month = 'Feb' THEN revenue ELSE null END) AS 'Feb_Revenue',
    SUM(CASE WHEN month = 'Mar' THEN revenue ELSE null END) AS 'Mar_Revenue',
    SUM(CASE WHEN month = 'Apr' THEN revenue ELSE null END) AS 'Apr_Revenue',
    SUM(CASE WHEN month = 'May' THEN revenue ELSE null END) AS 'May_Revenue',
    SUM(CASE WHEN month = 'Jun' THEN revenue ELSE null END) AS 'Jun_Revenue',
    SUM(CASE WHEN month = 'Jul' THEN revenue ELSE null END) AS 'Jul_Revenue',
    SUM(CASE WHEN month = 'Aug' THEN revenue ELSE null END) AS 'Aug_Revenue',
    SUM(CASE WHEN month = 'Sep' THEN revenue ELSE null END) AS 'Sep_Revenue',
    SUM(CASE WHEN month = 'Oct' THEN revenue ELSE null END) AS 'Oct_Revenue',
    SUM(CASE WHEN month = 'Nov' THEN revenue ELSE null END) AS 'Nov_Revenue',
    SUM(CASE WHEN month = 'Dec' THEN revenue ELSE null END) AS 'Dec_Revenue'
FROM Department
GROUP BY id
```

### Explicação
Seleciona-se o id e a soma de cada receita de cada mês como mês_Revenue.
Por fim, agrupa-pe por id.



## 1393. Capital Gain/Loss
Table: Stocks
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| stock_name    | varchar |
| operation     | enum    |
| operation_day | int     |
| price         | int     |
+---------------+---------+
```
(stock_name, operation_day) is the primary key for this table.
The operation column is an ENUM of type ('Sell', 'Buy')
Each row of this table indicates that the stock which has stock_name had an operation on the day operation_day with the price.
It is guaranteed that each 'Sell' operation for a stock has a corresponding 'Buy' operation in a previous day. It is also guaranteed that each 'Buy' operation for a stock has a corresponding 'Sell' operation in an upcoming day.
 

Write an SQL query to report the Capital gain/loss for each stock.

The Capital gain/loss of a stock is the total gain or loss after buying and selling the stock one or many times.

Return the result table in any order.


### Resolução
```
SELECT stock_name, SUM(total_sell) - SUM(total_buy) AS capital_gain_loss
FROM (
    SELECT stock_name,
        CASE WHEN operation = 'Buy' THEN price ELSE '0' END AS total_buy,
        CASE WHEN operation = 'Sell' THEN price ELSE '0' END AS total_sell
    FROM Stocks) a
GROUP BY 1
```

### Explicação
Primeiro, cria-se uma subquery, que seleciona o stock_name em dois casos, estes sendo:
 a. Caso a opção seja Buy, então price é chamado de total_buy.
 b. Caso a opção seja Sell, então price é chamado de total_sell.
Assim, na query principal, é selecionado o stock_name e a diferença da soma de todos total_sell com todos total_buy, nomeado de capital_gain_loss.
Por fim, é agrupado pelo stock_name


## 1407. Top Travellers
SQL Schema
Table: Users
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+
```
id is the primary key for this table.
name is the name of the user.
 

Table: Rides
```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| user_id       | int     |
| distance      | int     |
+---------------+---------+
```
id is the primary key for this table.
user_id is the id of the user who traveled the distance "distance".
 

Write an SQL query to report the distance traveled by each user.

Return the result table ordered by travelled_distance in descending order, if two or more users traveled the same distance, order them by their name in ascending order.




### Resolução
```
SELECT u.name AS name, IF(r.distance IS null, 0, SUM(r.distance)) AS travelled_distance
FROM Users u
LEFT JOIN Rides r ON u.id = r.user_id
GROUP BY name
ORDER BY travelled_distance DESC, name
```

### Explicação
Sendo a tabela User (u) e a tabela Rides (r), é selecionado u.name e travelled_distance, que é a expressão condicional e evidencia que, caso a r.distance seja nula, então o valor é 0 e, caso o contrário, o valor é a soma de r.distance.
Assim, junta-se à tebla Rides r, com um left join, de modo que u.id seja igual a r.user_Id.
Por fim, é agrupado por name e ordenado tanto por name, quanto por travelled_distance, em modo decrescente



## 1484. Group Sold Products By The Date
Table Activities:
```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| sell_date   | date    |
| product     | varchar |
+-------------+---------+
```
There is no primary key for this table, it may contain duplicates.
Each row of this table contains the product name and the date it was sold in a market.
 

Write an SQL query to find for each date the number of different products sold and their names.

The sold products names for each date should be sorted lexicographically.

Return the result table ordered by sell_date.


### Resolução
```
SELECT sell_date, COUNT(DISTINCT product) AS num_sold,
        GROUP_CONCAT(DISTINCT product
                     ORDER BY product) AS products
FROM Activities
GROUP BY sell_date
ORDER BY sell_date
```

### Explicação
Seleciona sell_date, COUNT(DISTINCT product), como num_sold, e a concatenação de produtos distintos, que são ordenamdos por product, como products.
Além disso, é agrupado e ordenado por sell_date.


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação


## 


### Resolução
```
```

### Explicação




