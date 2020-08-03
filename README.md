In this project, I am going to use Grafana and MariaDB to track monthly expenses.

Here are the main steps for setting-up MariaDB and Grafana on Centos 8:

1. install/config mariadb

 1.1 install mariadb, for CentOS 8 run

    "dnf install -y mariadb"

 1.2 setup / config mariadb

     systemctl enable --now mariadb
     mysql_secure_installation
 1.3 create database / read only user for Grafana

     mysql -u root -p
      >create database spending
      > CREATE USER 'grafanaReader' IDENTIFIED BY 'mypassword';
      >GRANT SELECT ON spending.* TO 'grafanaReader';
      
 1.4 download the sample-bill
 
 1.5 create table and load sample data
 
    mysql -uroot -ptest1234 spending <<EOF
    create table if not exists bill ( Year char(4) NOT NULL, Month char(3) NOT NULL, Name       varchar(25) NOT NULL, Amount float(10,2) NOT NULL, Paid varchar(3) NOT NULL, PRIMARY KEY (Year, Month, Name));
    load data local infile 'sample-bill' into table bill fields terminated by ':';
    EOF


2. install / setup grafana

2.1 install grafana server, for CentOS8 run

    "dnf install grafana"

2.2 enable / start grafana server

    systemctl enable --now grafana-server

2.3 setup data source

    go to http://localhost:3000

    login as admin/admin

    change password and setup data source
    setting ---> "Data Sources" ---> "Add Data Source" ---> select "MySQL" ---> fill the following fields under "MySQL Connection", 
    "Host" "Database" "User" "Password" ---> Click "Save & Test"

2.4 import grafana dashboard 

    download Expense.json and import to Grafana dashboard

    click "Grafana" icon on the top left ---> Home ---> Import Dashboard ---> upload .json file ---> and select the file dowbloaded above ---> click "Import"
   

