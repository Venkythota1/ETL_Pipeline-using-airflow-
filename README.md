**🧩 Overview**

This project demonstrates how to build a data pipeline using Apache Airflow to scrape Amazon Data Engineering books and store the results in a PostgreSQL database.

All components are open-source and run locally using Docker containers.

**⚙️ Architecture**

Tools Used:

Apache Airflow → Workflow orchestration tool (scheduler)

PostgreSQL → Relational database for storing data

pgAdmin → GUI for managing PostgreSQL

Docker Compose → To manage and run all containers together

**🧠 Airflow Components**
Component	Description
Scheduler	Schedules and triggers the execution of DAGs
Webserver	Runs the Airflow UI for monitoring DAGs
Metadata DB	Stores DAG runs, task status, and configuration data
Executor	Executes the tasks defined in the DAG

A DAG (Directed Acyclic Graph) represents a workflow — a collection of tasks executed in a specific order.

🐳 Setup: Airflow on Docker

We use Docker Compose to run Airflow, PostgreSQL, and pgAdmin containers together.

**🔗 Reference**

Official Airflow on Docker Documentation

**🧾 Steps**

Install Airflow on Docker

docker compose up airflow-init
docker compose up


Install PostgreSQL and pgAdmin on Docker
Both are included in the same docker-compose.yml file or can be added as separate services.

Verify Running Containers

docker ps
docker container ls


Inspect PostgreSQL Container
To connect from pgAdmin or Airflow:

docker inspect <container_id>


Copy the IP Address of the PostgreSQL container.

Connect pgAdmin to PostgreSQL
Use the container IP and port from the inspect command.

Create Database
In pgAdmin, create a new database named:

amazon_books

**📄 DAG Description**

The Airflow DAG performs the following tasks:

Extract

Scrapes book data (title, author, price, and rating) from Amazon.

Fetches top Data Engineering books.

Transform

Removes duplicates.

Cleans and formats the data into a Pandas DataFrame.

Load

Uses PostgresHook to connect to PostgreSQL.

Inserts the data into the books table.

**🧠 Workflow Summary**
Step	Task	Description
1	scrape_books	Scrape book data from Amazon
2	clean_books	Clean and deduplicate data
3	load_to_postgres	Insert cleaned data into PostgreSQL using PostgresHook

🗄️ Example Table Schema (books)
Column	Type	Description
title	TEXT	Book title
author	TEXT	Book author
price	FLOAT	Price of the book
rating	FLOAT	Average rating

🧰 Airflow Connection Setup

In the Airflow UI:

Navigate to Admin → Connections → +

Create a new connection:

Conn Id: postgres_conn

Conn Type: Postgres

Host: <PostgreSQL container IP>

Schema: amazon_books

Login: postgres

Password: <your_password>

Port: 5432
