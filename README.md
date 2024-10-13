# Advance_SQL

## Key Topics Covered:

- Window Functions (ROW_NUMBER, RANK, DENSE_RANK, NTILE)
- Subqueries and Correlated Subqueries
- Common Table Expressions (CTE) and Recursive Queries
- Temp Tables
- Query Optimization

Each section includes detailed examples, explanations, and hands-on practice problems to deepen understanding of advanced SQL concepts, using PostgreSQL.

### Installing  the SQL Server Express 2022

* [SQL server 2012](https://go.microsoft.com/fwlink/p/?linkid=2216019&clcid=0x4009&culture=en-in&country=in) It is the free latest version of SQL sever express officially by Microsoft and we will be using that throught out the learning

### Installing SQL Service Management Studio [GUI]

* [SSMS download link](https://aka.ms/ssmsfullsetup) Download using the link provided

* Install the SSMS and open it
  
  * Change the Encryption option from `Mandatory` to `Optional` -> The press connect

  ![Installing  the SQL Server Express 2022](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-11-32-07-image.png)

### Setting Up the Database

- Download the database from [AdventureWorks]([AdventureWorks sample databases - SQL Server | Microsoft Learn](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms)) [Download the OLTP version]

- Copy the download `.bak` file and go to
  
  - C:\Program Files\Microsoft SQL Server\MSSQL16.SQLEXPRESS\MSSQL\Backup
  
  - Past the `.bak` file in the above location

- Now open SSMS -> Right of Database and click `Restore Database`
  
  ![Setting Up the Database](https://github.com/HBISHT233/Advance_SQL/blob/main/Images/2024-10-13-11-35-37-image.png)

- Restore the database to your SQL application