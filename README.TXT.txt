git clone https://daniboy1982@bitbucket.org/daniboy1982/lab-mule1.git 

mysql -u root -p

create database

create user fiapusr identified by 'fiap2019';

 use fiapdb;

CREATE TABLE ALUNO
 (rm VARCHAR(50) NOT NULL, 
nome VARCHAR(50) NOT NULL,
telefone VARCHAR(25), 
EMAIL VARCHAR(25));


local do driver

C:\Program Files\Docker Toolbox\lab-mule1

 grant all privileges on fiapdb.* to fiapusr@'%';

insert into ALUNO values ('1','daniel','11976772690','daniel.lemes@gmail.com');


version: "3"
services:
  db-mule:
    image: mysql:5.7
    command: --default-authentication-plugin=mysql_native_password
    ports: 
     - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: "fiap2019"
      MYSQL_DATABASE: "fiap"
  nginx:
    image: nginx:latest
    ports:
     - "85:80"




#[message.inboundProperties.'http.method' == 'GET']


INSERT INTO ALUNO VALUES 
(#[message.payload.rm], 
#[message.payload.nome], 
#[message.payload.telefone], 
#[message.payload.EMAIL])





--------------------------------------------------
--------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>

<mule xmlns:tracking="http://www.mulesoft.org/schema/mule/ee/tracking" xmlns:db="http://www.mulesoft.org/schema/mule/db" xmlns:json="http://www.mulesoft.org/schema/mule/json" xmlns:http="http://www.mulesoft.org/schema/mule/http" xmlns="http://www.mulesoft.org/schema/mule/core" xmlns:doc="http://www.mulesoft.org/schema/mule/documentation"
	xmlns:spring="http://www.springframework.org/schema/beans" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-current.xsd
http://www.mulesoft.org/schema/mule/core http://www.mulesoft.org/schema/mule/core/current/mule.xsd
http://www.mulesoft.org/schema/mule/http http://www.mulesoft.org/schema/mule/http/current/mule-http.xsd
http://www.mulesoft.org/schema/mule/db http://www.mulesoft.org/schema/mule/db/current/mule-db.xsd
http://www.mulesoft.org/schema/mule/json http://www.mulesoft.org/schema/mule/json/current/mule-json.xsd
http://www.mulesoft.org/schema/mule/ee/tracking http://www.mulesoft.org/schema/mule/ee/tracking/current/mule-tracking-ee.xsd">
    <http:listener-config name="HTTP_Listener_Configuration" host="0.0.0.0" port="8081" doc:name="HTTP Listener Configuration"/>
    <db:mysql-config name="MySQL_Configuration" host="192.168.99.101" port="3306" user="fiapusr" password="fiap2019" database="fiapdb" doc:name="MySQL Configuration"/>
    <flow name="alunoflowFlow">
        <http:listener config-ref="HTTP_Listener_Configuration" path="/aluno" allowedMethods="GET,POST" doc:name="HTTP"/>
        <logger message="Funcionou" level="INFO" doc:name="Logger"/>
        <choice doc:name="Choice">
            <when expression="#[message.inboundProperties.'http.method' == 'GET']">
                <db:select config-ref="MySQL_Configuration" doc:name="Database">
                    <db:parameterized-query><![CDATA[select * from ALUNO;]]></db:parameterized-query>
                </db:select>
                <json:object-to-json-transformer doc:name="Object to JSON"/>
            </when>
            <otherwise>
                <json:json-to-object-transformer returnClass="java.lang.Object" doc:name="JSON to Object"/>
                <db:insert config-ref="MySQL_Configuration" doc:name="insert aluno">
                    <db:parameterized-query><![CDATA[INSERT INTO ALUNO VALUES 
(#[message.payload.rm], 
#[message.payload.nome], 
#[message.payload.telefone], 
#[message.payload.EMAIL])]]></db:parameterized-query>
                </db:insert>
                <json:object-to-json-transformer doc:name="Object to JSON"/>
            </otherwise>
        </choice>
    </flow>
</mule>
