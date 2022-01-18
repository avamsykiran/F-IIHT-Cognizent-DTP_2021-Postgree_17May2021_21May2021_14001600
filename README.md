
PostgreSQL ToC
-----------------------------------------------------------------------------------------------------------------
How to create a database from scratch. 
How to retrieve data from tables using select queries. 
Filter data using where clauses in SQL.
Build complex SQL queries to extract important data from databases using table joins.
Use SQL aggregate functions and group data using group by clauses.
How to create, alter and delete tables from a database in PostgreSQL. 
How to insert, update and delete data from tables in PostgreSQL

PostGree SQL
--------------------------------

    RDBMS Product
    1. SQL
    2. Pretty light weight and Strongly deeply typed .

    Lab Setup

        PostGreeSQL 

    SQL
    ------------------------------------------------------
        DDL     Data Definition Language

                        CREATE
                        DROP
                        ALTER

        DML     Data Manipulation Language

                        INSERT
                        UPDATE
                        DELETE

        DRL     Data Retrival Language

                        SELECT 
                            FROM
                            WHERE
                            GROUP BY
                            HAVING
                            ORDER BY

                        JOINS
                        SubQrys
                        Correlated Sub QRYS
                        Aggregate Functions
                              
        DCL     Data Control Language

                        GRANT
                        REVOKE

        TCL     Transaction Control Language

                        SAVEPOINT
                        ROLLBACK
                        COMMIT

    Data Definition Language
    -------------------------------------------------------------------------------
    
    psql -h localhost -U postgres       launch psql as 'postgres' user


    \l                  list all databases

    CREATE DATABASE dbName

    \c dbName           connect to a database


    Table
        a group of entities represented through relation/tuple/record/row
                                                    set/attribute/field/col

    Table = Headspace/Schema/Structure + dataspace

    CREATE TABLE tableName (
        col1    datatype    constraint,
        col2    datatype    constraint,
        ...............................
    );

    datatype
    ----------------------------------------------

        int
        smallint
        bigint

        decimal
        precision
        numeric

        money

        char
        varchar
        text

        bool

        xml
        json

        array

        date
        time
        datetime
        timestamp

    constraint
    --------------------------------------------------

        constraints are kind of rules we impose on our table columns, to govern the
        acceptable domain of a column.

        constraints ensure the data validity.

        Domain Constraints      ensures Domain Integrity
            
            check               clazz  check between 1 and 12
            default
            not null

        Entity Constraints      ensures Entity Integrity

            check               check doj>dob
            unique              ensure the given col has no duplicate entires eg: mobile,emailid
            primary key         ensure the that specific col acts as identifier to the record.
                                    that the primary key can never be null, and as well it can never allow duplicate values.

            composite key       a group of columns act as primary key.

                                    Employees   empId   name    sal     job     doj

                                                (PK)

                                    Books       author title   price   noOCopies   subject     

                                                (   PK - CK )
                                    
                                    Books       bcode   author title   price   noOCopies   subject     

                                                ( PK )

            fareign key         is a link such that one of the columns of a table 
                                refers to an other colmn (most pk col) of another table.

                                    ensures the entity relationship integrity.

                                    deptId of employees table can be made to refer to
                                    the deptId of departments table.\

                                    so that no employee is added to a department that does not exist.

        
        depts       deptId, deptName, location
        emps        empId, empName , basic, joindate, desg, deptId


        CREATE TABLE depts ( 
            deptid int primary key,
            deptName varchar(30) NOT NULL,
            location varchar(30) NOT NULL
        );

        CREATE TABLE emps(
            empId int primary key,
            empName varchar(25) not null,
            basic money not null,
            joindate date not null,
            desg varchar(25) not null,
            deptId int not null,
            CONSTRAINT FK_DEPT_EMPS FOREIGN KEY(deptId) REFERENCES depts(deptId) 
        );

\d tableName            gives teh schema/structure of a table

DROP TABLE tableName;

DROP DATABASE databaseName;

ALTER TABLE tableName ADD colName datatype constraint;
ALTER TABLE tableName DROP COLUMN colName;
ALTER TABLE tableName ALTER COLUMN colName TYPE datatype;
ALTER TABLE tableName ADD CONSTRAINT constraintName .....;
ALTER TABLE tableName DROP CONSTRAINT constraintName;

Data Manipulation Language
----------------------------------------------------------------------
    INSERT

                INSERT INTO tableName
                VALUES(VA1,VA2,VA3.......)

                INSERT INTO tableName(col1,col2,col3...)
                VALUES(VA1,VA2,VA3.......)

    UPDATE

                UPDATE tableName
                SET col1=val1,col2=val2.....
                [WHERE cond];

    DELETE
                DELETE FROM tableName [WHERE cond]


Data Retrival Language
----------------------------------------------------------------------------------------

        SELECT *
        FROM tableName

        Projection

            SELECT col1,col2,expression AS soemcolLabel
            FROM tableName

            SELECT EMPNO,ENAME,SAL,COMM,sal+coalesce(comm,cast(0 as money)) as totalSal 
            FROM EMP;

        Filtering

            SELECT col1,col2,expression AS soemcolLabel
            FROM tableName
            WHERE cond;

            =   !=  <>  <   >   <=  >=  between..and..  like

            and or not

        Grouping

            Aggregate Function
                COUNT
                SUM
                MIN
                MAX
                AVG

            SELECT col1,aggregateFunction1,...
            FROM tableName
            GROUP BY col1;

        Filter on Aggregates
            
            SELECT col1,aggregateFunction1,...
            FROM tableName
            WHERE cond-on-non-aggreagte-values
            GROUP BY col1
            HAVING cond-on-aggregate-valeus;

        Ordering / Sorting

            SELECT col1,aggregateFunction1,...
            FROM tableName
            WHERE cond-on-non-aggreagte-values
            GROUP BY col1
            HAVING cond-on-aggregate-valeus
            order by col;

    
    Joins
    --------------------------------------------------------------------

        NATURAL JOIN

                SELECT col1....
                FROM tabl1 NATURAL JOIN table2

        INNER JOIN
        
                SELECT col1....
                FROM table1 INNER JOIN table2 ON table1.col = table2.col

        OUTER JOIN
        
                SELECT col1....
                FROM table1 LEFT OUTER JOIN table2 ON table1.col = table2.col

                SELECT col1....
                FROM table1 RIGHT OUTER JOIN table2 ON table1.col = table2.col
                
                SELECT col1....
                FROM table1 FULL OUTER JOIN table2 ON table1.col = table2.col

        
        Retrive no of subordinates for each and every employeee
        -----------------------------------------------------------------

            SELECT m.ename as MGR_NAME,COUNT(*)-1 as SUBORDINATE_COUNT
            FROM emp e RIGHT OUTER JOIN emp m ON e.mgr = m.empno
            GROUP BY m.ename;

        Retrive no of emps for each and every dname
        -----------------------------------------------------------------

              select dname,count(*) from emp INNER JOIN dept ON emp.deptno = dept.deptno group by dname;   


        Retrive the data from emp table as below

            EMPNO   ENAME   EMP_SAL     MGR_NAME    MGR_SAL     DIFF
            -----------------------------------------------------------------------

            select e.empno, e.ename , e.sal as emp_sal, m.ename as mgr_name,m.sal as mgr_sal, m.sal-e.sal as Diff 
            from emp e join emp m on e.mgr=m.empno;

Sub Query
=====================================================================================

        Retrive the name of the employee who has the maximum sal.
        --------------------------------------------------------------------
        select ename,sal 
        from emp 
        where sal = (select max(sal) from emp)

        Retrive the name of the senior most employee
        --------------------------------------------------------------------
        select ename from emp where hiredate = (select min(hiredate) from emp);

Corelated Sub Query
=====================================================================================

        Retrive the dname and ename of the senior most employee in each dept
        -----------------------------------------------------------------------------

            SELECT e.ename,d.dname,e.hiredate
            FROM emp e JOIN dept d ON e.deptno = d.deptno
            WHERE e.hiredate = (select min(e2.hiredate) from emp e2 where e2.deptno=e.deptno );


    Retrive the name of the employee having the highest number of repotees.
    ----------------------------------------------------------------------------

            SELECT m.ename as MGR_NAME,COUNT(*) as SUBORDINATE_COUNT
            FROM emp e JOIN emp m ON e.mgr = m.empno
            GROUP BY m.ename;

Postgree Built-in functions
=====================================================================================
   
    string functions:
    ==================
    1) length(string)                          SELECT length('Vamsy Kiran');
    2) lower(string)
    3) upper(string)
    4) ltrim(string)
    5) rtrim(string)
    6) trim(string)
    7) lpad(string, length, pad_string)        SELECT lpad('Vamsy', 8, 'A');
    8) rpad(string, length, pad_string)
    9) initcap(string)
    10) substring( string [from start_position] [for length] )
        SELECT substring('Vamsy Kiran' from 1 for 4);
    11) repeat(string, number)
    12) replace( string, from_substring, to_substring )

    numeric/math functions:
    =======================
    1) abs(number)
    2) mod(n, m)
    3) power(m, n)
    6) sign(n)
    7) sqrt(n)
    8) ceiling(n)
    9) floor(n)
    10) round( number, [ decimal_places ] )
    11) trunc( number, [ decimal_places ] )

    date functions:
    ===============
    18) current_date
        SELECT current_date;

        SELECT current_date + 1;
    19) current_time
        SELECT current_time;
    20) now
        SELECT now();
    21) age( [date1,] date2 )

        SELECT age(timestamp '2014-01-01'); 	/* from current date */
        SELECT age(timestamp '2014-04-25', timestamp '2014-01-01');
        SELECT age(timestamp '2014-04-25', timestamp '2014-04-17');
        SELECT age(current_date, timestamp '2012-09-16');

    conversion functions:
    =====================
    22) to_char( value, format_mask )

        With numbers:
        -------------
        Parameter	Explanation
        ----------------------------
        9	Value (with no leading zeros)
        0	Value (with leading zeros)
        .	Decimal
        ,	Group separator
        PR	Negative value in angle brackets
        S	Sign
        L	Currency symbol
        D	Decimal
        G	Group separator
        MI	Minus sign (for negative numbers)
        PL	Plus sign (for positive numbers)
        SG	Plus/minus sign (for positive and negative numbers)
        RN	Roman numerals
        TH	Ordinal number suffix
        th	Ordinal number suffix
        V	Shift digits
        EEEE	Scientific notation

            SELECT to_char(1210, '9999.99');
            1210.00

            SELECT to_char(1210.7, '9G999.99');
            1,210.70

            SELECT to_char(1210.7, 'L9G999.99');
            $ 1,210.70

            SELECT to_char(121, '9 9 9');
            1 2 1

            SELECT to_char(121, '00999');
            00121


        with dates:
        -----------

        Parameter	Explanation
        ----------------------------
        YYYY	4-digit year
        Y,YYY	4-digit year, with comma
        YYY
        YY
        Y	Last 3, 2, or 1 digit(s) of year
        IYYY	4-digit year based on the ISO standard
        IYY
        IY
        I	Last 3, 2, or 1 digit(s) of ISO year
        Q	Quarter of year (1, 2, 3, 4; JAN-MAR = 1).
        MM	Month (01-12; JAN = 01).
        MON	Abbreviated name of month in all uppercase
        Mon	Abbreviated name of month capitalized
        mon	Abbreviated name of month in all lowercase
        MONTH	Name of month in all uppercase, padded with blanks to length of 9 characters
        Month	Name of month capitalized, padded with blanks to length of 9 characters
        month	Name of month in all lowercase, padded with blanks to length of 9 characters
        RM	Month in uppercase Roman numerals
        rm	Month in lowercase Roman numerals
        WW	Week of year (1-53) where week 1 starts on the first day of the year
        W	Week of month (1-5) where week 1 starts on the first day of the month
        IW	Week of year (01-53) based on the ISO standard
        DAY	Name of day in all uppercase, padded with blanks to length of 9 characters
        Day	Name of day capitalized, padded with blanks to length of 9 characters
        day	Name of day in all lowercase, padded with blanks to length of 9 characters
        DY	Abbreviated name of day in all uppercase
        Dy	Abbreviated name of day capitalized
        dy	Abbreviated name of day in all lowercase
        DDD	Day of year (1-366)
        IDDD	Day of year based on ISO year
        DD	Day of month (01-31)
        D	Day of week (1-7, where 1=Sunday, 7=Saturday)
        ID	Day of week based on ISO year (1-7, where 1=Monday, 7=Sunday)
        J	Julian day; the number of days since midnight on November 24, 4714 BC
        HH	Hour of day (01-12)
        HH12	Hour of day (01-12)
        HH24	Hour of day (00-23)
        MI	Minute (00-59)
        SS	Second (00-59)
        MS	Millisecond (000-999)
        US	Microsecond (000000-999999)
        SSSS	Seconds past midnight (0-86399)
        am, AM, pm, or PM	Meridian indicator
        a.m., A.M., p.m., or P.M.	Meridian indicator
        ad, AD, a.d., or A.D	AD indicator
        bc, BC, b.c., or B.C.	BC indicator
        TZ	Name of time zone in uppercase
        tz	Name of time zone in lowercase
        CC	2-digit century


        SELECT to_char(date '2014-04-25', 'YYYY/MM/DD');
        2014/04/25

        SELECT to_char(date '2014-04-25', 'MMDDYY');
        042514

        SELECT to_char(date '2014-04-25', 'Month DD, YYYY');
        April     25, 2014


    23) to_date( string1, format_mask )

        SELECT to_date('2014/04/25', 'YYYY/MM/DD');
        to_date
        ------------
        2014-04-25

        postgres=# SELECT to_date('033114', 'MMDDYY');
        to_date
        ------------
        2014-03-31

        postgres=# SELECT to_date('February 08, 2014', 'Month DD, YYYY');
        to_date
        ------------
        2014-02-08


    24) to_number( string1, format_mask )

        SELECT to_number('1210.73', '9999.99');
        to_number
        -----------
        1210.73

        postgres=# SELECT to_number('1,210.73', '9G999.99');
        to_number
        -----------
        1210.73

        postgres=# SELECT to_number('$1,210.73', 'L9G999.99');
        to_number
        -----------
        1210.73

        postgres=# SELECT to_number('$1,210.73', 'L9G999');
        to_number
        -----------
            1210
==============================================================================

CREATE TABLE orders (
	info json NOT NULL
);

Insert JSON data:
-----------------
INSERT INTO orders (info)
VALUES('{ "customer": "Srinivas", "items": {"product": "keyboard","qty": 2}}');

Querying JSON data:
-------------------
SELECT info FROM orders;
