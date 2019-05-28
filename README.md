# mssql-px

A test that does the following:


1. Run mssql in a container. This test uses version : `microsoft/mssql-server-linux:2017-CU13`
2. Back this container with a portworx volume
3. Generate IOs i.e. write data into the database
4. Simulate a PX failure on the node where the database server is running


Commands:
1. Start the database server
```
pxctl v c --size 50 --repl 3 mssqlvol

docker run --volume-driver pxd -v mssqlvol:/var/opt/mssql -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1' -p 1433:1433 --name sql1 -d  microsoft/mssql-server-linux:2017-CU13
```

2. Get into the DB Server
```
docker exec -it sql1 bash

/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Password1'
```

3. Create a Test Database
```
CREATE DATABASE TestDB

SELECT Name from sys.Databases

GO
```

4. Insert Data
```
USE TestDB

CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)

INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);

GO
```

5. Query Data
```
SELECT * FROM Inventory WHERE quantity > 152;

GO
```


Output Capture:
```
1> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (3, 'Mango', 160);
2> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (4, 'Apple', 177);
3> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (5, 'Cherry', 150);
4> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (6, 'BlackBerry', 50);
5>
6>
7> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (6, 'BlackBerry', 70);
8> GO


1> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (6, 'BlackBerry', 50);
2> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (7, 'aa', 70);
3> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (8, 'aa1', 70);
4> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (9, 'aa2', 170);
5> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (10, 'aa3', 170);
6> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (11, 'aa4', 170);
7> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (12, 'aa4', 170);
8> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (13, 'aa4', 170);
9> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (14, 'aa4', 170);
10> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (15, 'aa4', 170);
11> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (16, 'aa4', 170);
12> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (17, 'aa4', 170);
13> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (18, 'aa4', 170);
14> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (19, 'aa4', 170);
15> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (20, 'aa4', 170);
16> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (21, 'aa4', 170);
17> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (22, 'aa4', 170);
18> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (23, 'aa4', 170);
19> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (24, 'aa4', 170);
20> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (25, 'aa4', 170);
21>
22> GO

(1 rows affected)

(1 rows affected)

(1 rows affected)
(1 rows affected)
1> SELECT * FROM Inventory WHERE quantity > 152;
2> GO
id          name                                               quantity
----------- -------------------------------------------------- -----------
          2 orange                                                     154
          3 Mango                                                      160
          4 Apple                                                      177
          9 aa2                                                        170
         10 aa3                                                        170
         11 aa4                                                        170
         12 aa4                                                        170
         13 aa4                                                        170
         14 aa4                                                        170
         15 aa4                                                        170
         16 aa4                                                        170
         17 aa4                                                        170
         18 aa4                                                        170
         19 aa4                                                        170
         20 aa4                                                        170
         21 aa4                                                        170
         22 aa4                                                        170
         23 aa4                                                        170
         24 aa4                                                        170
         25 aa4                                                        170
         25 aa4                                                        170

(21 rows affected)
```

Credit: Using https://github.com/twright-msft/mssql-node-docker-demo-app.git
