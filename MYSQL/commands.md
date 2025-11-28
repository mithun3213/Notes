to sign in --> mysql -u root
to create the database --- > create database kct;
show the database --> show databases;
create table into kct ---> create table info (
name varchar(50) not null,
email varchar(50) not null,
dept_name(50) not null
);
to insert the value into info --->INSERT INTO table_name (column1, column2, column3)
VALUES (value1, value2, value3);

select * from info ;
 to add the extra ALTER TABLE info
    -> add salary int;
to add the value of the salary  update info
    -> set salary=9000;
    