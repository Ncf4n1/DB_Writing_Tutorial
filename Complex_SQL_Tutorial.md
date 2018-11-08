# __Introduction__
This tutorial is designed to discuss effective techniques to decomposing complex statements using Standard Query Language (SQL). Specifically, we will look at how to attack a seemingly overwhelming complex database query that will require the use of several SQL statements and SQL's inner join ability. By the end of this tutorial, we hope that you will be able to look a complex query, break it down into its simpler parts, and construct a SQL statement piece by piece.

## __Prerequisites__
Before we step into a brief review of SQL select and join statements, we are assuming that you have previous knowledge on Relational Databases and a brief introduction to the basic syntax of SQL, though not necessarily any practice with constructing moderate to complex SQL queries.

# __SQL Select and Join Statements Review__
Before moving on to an example, we will intoduce a few SQL commands that are neccessary for a better understanding of next the next section.<br>
<b>JOIN: </b> <br>
A JOIN statement combines the data from one table with another based on a given common column between them.
Common types of join: INNER JOIN, LEFT JOIN, RIGHT JOIN.

<b>GROUP BY [column-1], [column-2]: </b> <br>
The GROUP BY statement is used to summarize the data recieved into columns that follow it. It returns only one row for duplicate results and is usually used with aggregate functions.

<b>AVG(x): </b><br>
AVG() is an aggregate function. Aggregate functions are used to perform calculations on a given table or column and return the result. In the case of AVG(), it returns the average number.

# __Company Database Examples__
To better illustrate the utility of using join and select operation in SQL to formulate complex queries, we will walk through an example. This example is taken from the COMPANY database referenced in the course textbook. The COMPANY database contains tables detailing data about employees, departments, projects, and employee dependents, etc. A snapshot of the relational schema is shown below in Figure 1. For a detailed description of the COMPANY database, please refer to chapter 2 of the textbook.

![](https://github.com/Ncf4n1/DB_Writing_Tutorial/blob/master/Screen%20Shot%202018-11-06%20at%208.22.46%20PM.png?raw=true)
Figure 1: COMPANY relational schema <sup> 1 </sup>


One question that might arise for a data analyst with access to such database might be: what department in the company has the highest salary gap between male and female workers? To find the answer to this question, we need to able to use nested select statements, UNION operations and aggregation operations.

As a first step, we will load the Employee table using the following SQL command:

> select * from Employee;

This produces the following table:

![](https://github.com/Ncf4n1/DB_Writing_Tutorial/blob/master/Screen%20Shot%202018-11-06%20at%207.53.37%20PM.png?raw=true)


Now, we would like to summarize the salary data in this table, so that we have the average salary for males and females of every department. To accomplish this, we use the group by operation at the end of select statements to pick the criteria by which we indent to aggregate the data. Furthermore, we use avg() to average the salaries. The following SQL line can be used to obtain the average salary for each department,sex pair.

> select Dno,Sex,avg(Salary) from Employee group by Dno,Sex;

![](https://github.com/Ncf4n1/DB_Writing_Tutorial/blob/master/Screen%20Shot%202018-11-06%20at%208.41.54%20PM.png?raw=true)

At this point, we need to combine male and female data for each department and compare their earnings(note that this will exclude Depratment 1 which has only one employee). To do this, we need to retrieve the male and female table separately:
> select Dno,Sex,avg(Salary) from Employee where Sex = 'M' group by Dno,Sex ;

> select Dno,Sex,avg(Salary) from Employee where Sex = 'F' group by Dno,Sex ;


This is where the JOIN operations are useful. We can attach these two tables side by side, so that each column contain the female and male salaries for a certain department. We will join the two tables on a common attribute they have, which is Dno. Lastly, we can select the difference (using the - operator) between any two attributes in a table. Thus, we will calculate the different between the male and female salaries from the table as follows:

> select DnoMale as Dno, MaleAvgSalary, FemaleAvgSalary,  
(MaleAvgSalary)-(FemaleAvgSalary) from  
(select Dno as DnoMale, Sex, avg(Salary) as MaleAvgSalary from Employee  
where Sex = 'M' group by Dno, Sex),  
(select Dno as DnoFemale, Sex, avg(Salary) as FemaleAvgSalary from Employee  
where Sex = 'F' group by Dno, Sex)  
on DnoMale = DnoFemale;




![](https://github.com/Ncf4n1/DB_Writing_Tutorial/blob/master/Screen%20Shot%202018-11-06%20at%208.56.38%20PM.png?raw=true)


It would be helpful now to have the name of the department shown in the table. The best way to do this is to join the last table with the department table on the Dno attribute


![](https://github.com/Ncf4n1/DB_Writing_Tutorial/blob/master/Screen%20Shot%202018-11-06%20at%208.59.47%20PM.png?raw=true)

Finally, we need a command that will only return the highest difference between male and female workers. To achieve this, we only need to use the max command on the difference between salaries in the select statement:

> select Dname, DnoMale as Department_Number, MaleAvgSalary, FemaleAvgSalary,  
max((MaleAvgSalary) - (FemaleAvgSalary)) as from Department,  
(select DnoMale, MaleAvgSalary, FemaleAvgSalary,  
(MaleAvgSalary) - (FemaleAvgSalary) from  
(select Dno as DnoMale, Sex, avg(Salary) as MaleAvgSalary from Employee  
where Sex = 'M' group by Dno, Sex),  
(select Dno as DnoFemale, Sex, avg(Salary) as FemaleAvgSalary from Employee  
where Sex = 'F' group by Dno, Sex)  
on DnoMale = DnoFemale)  
on Dnumber = DnoMale;


# __Other Resources__
[Constructing Nested Queries](https://community.modeanalytics.com/sql/tutorial/sql-subqueries/)

[Improving Your Queries](https://www.datacamp.com/community/tutorials/sql-tutorial-query)

[More on Constructing Sub-Queries and Self Joins](https://ocw.mit.edu/courses/urban-studies-and-planning/11-521-spatial-database-management-and-advanced-geographic-information-systems-spring-2003/lecture-notes/lect4.pdf)

# __References__
SQL information, example data and database, and data images used from: </br>
[1] Elmasri, Ramez, and Shamkant B. Navathe. Fundamentals of Database Systems. Pearson, 2017.

Tutorial formatting ideas seen from: </br>
[2] tutorialspoint.com. “SQL Tutorial.” Www.tutorialspoint.com, www.tutorialspoint.com/sql/.
