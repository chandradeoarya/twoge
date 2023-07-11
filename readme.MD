# **Twoge**

<h1 align="center">
	<img
		width="500"
		alt="Project Twoge"
		src="./static/img/twoge-cover.png">
</h1>

Twoge is a social media platform dedicated solely to tweets about Dodge. This application is built using Flask, SQLAlchemy, and PostgreSQL.

<h1 align="center">
	<img
		width="100"
		alt="Project Twoge"
		src="./static/img/twoge.png">
</h1>

## **Prerequisites**

Before you can launch Twoge, you will need to have the following software installed on your system:

- Python 3
- Flask
- SQLAlchemy
- PostgreSQL


## **Getting started**

To get started with Twoge, follow these steps:

1. Clone the repository to your local machine.
2. Create a virtual environment for the project and activate it.
3. Install the required packages using **`pip install -r requirements.txt`**.
4. Create a PostgreSQL database for the application.
5. Set the **`DATABASE_URL`** environment variable to the URL of your PostgreSQL database.
6. Run the application using **`python app.py`**.
7. Navigate to **`http://localhost:5000`** in your web browser to use Twoge.

## **Table attributes**

Twoge has a single table in its database, which stores information about each tweet. The table has the following attributes:

- **`date_posted`**: The date and time when the tweet was posted.
- **`author`**: The user who posted the tweet.
- **`title`**: The title of the tweet.
- **`content`**: The content of the tweet.


## **Objective**

The objective of this blog is to show how to dockerize a Flask application using Docker and Docker Compose. By the end of this blog, we will be able to create a Dockerfile to package a Flask app, build a Docker image, and run the application using Docker Compose.

### **Step Docker: Create a Dockerfile**

1. Create a new file called **`Dockerfile`** in the project directory.
2. Open the **`Dockerfile`** and add the following code:

```bash
FROM python:alpine
ENV SQLALCHEMY_DATABASE_URI=

RUN apk update && \
    apk add --no-cache build-base libffi-dev openssl-dev
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 8080
CMD python app.py
```

### **Step 3: Build the Docker Image**

1. Open a terminal and navigate to the project directory if you are not already there.
2. Build the Docker image using the following command:

```bash
docker build -t twoge-docker .
```

### **Step 4: Run the Docker Image**

1. After the image has been successfully built, run the Docker container using the following command:

```bash
docker run -p 8000:8000 twoge-docker -env SQLALCHEMY_DATABASE_URI=YOUR_DB_URI
```

1. You have to ensure you have database running in a separate container. 

### Docker compose

1. Create a new file called **`docker-compose.yml`** in the project directory.
2. Open the **`docker-compose.yml`** file and add the following code:

```yaml
version: "3.7"

services:
    database:
        image: postgres:latest
        container_name: twoge-database-server
        environment:
            POSTGRES_DB: twoge_db
            POSTGRES_USER: chandra
            POSTGRES_PASSWORD: akjA8iLp2A
        ports:
          - 5432:5432
        networks:
            - twoge_app_network
        volumes:
          - pgdata:/var/lib/postgresql/data
        healthcheck:
          test: ["CMD", "pg_isready", "-U", "chandra", "-d", "twoge_db"]
          interval: 10s
          timeout: 5s
          retries: 3

    app:
        build: .
        restart: always
        container_name: twoge-app
        ports:
          - 80:8080
        networks:
            - twoge_app_network
        volumes:
            - .:/app
        environment:
            SQLALCHEMY_DATABASE_URI: "postgresql://chandra:akjA8iLp2A@database:5432/twoge_db"
        depends_on:
          database:
            condition: service_healthy

networks:
    twoge_app_network:
        driver: bridge

volumes:
  pgdata:
```