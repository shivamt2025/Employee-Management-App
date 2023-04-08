After the third task we need to launch two more containers of rails application and configure Nginx to load balance incoming requests between three containers
Made a new directory for new setup and cd into it
created a docker-compose.yml file in the working directory with following config
version: "3"

services:
  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase

  web:
    build: .
    command: bundle exec rails server -p 3000 -b '0.0.0.0'
    environment:
      DATABASE_URL: postgres://myuser:mypassword@db/mydatabase
    depends_on:
      - db
    ports:
      - "3000"

  web2:
    build: .
    command: bundle exec rails server -p 3000 -b '0.0.0.0'
    environment:
      DATABASE_URL: postgres://myuser:mypassword@db/mydatabase
    depends_on:
      - db
    ports:
      - "3001"

  web3:
    build: .
    command: bundle exec rails server -p 3000 -b '0.0.0.0'
    environment:
      DATABASE_URL: postgres://myuser:mypassword@db/mydatabase
    depends_on:
      - db
    ports:
      - "3002"

  nginx:
    image: nginx:latest
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "8080:80"
    depends_on:
      - web
      - web2
      - web3
      
      This docker-compose.yml file defines five services: a database container using the latest version of the Postgres image, and three web containers running the Rails application. Each web container is linked to the database container and exposed on a different port. The Nginx container uses the latest version of the Nginx image and is linked to all three web containers. 
      It also mounts an nginx.conf file that i created next called ngix.config which is as follows-:
      events { }

http {
  upstream rails_servers {
    server web:3000;
    server web2:3000;
    server web3:3000;
  }

  server {
    listen 80;
    server_name localhost;

    location / {
      proxy_pass http://rails_servers;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
This nginx.conf file defines an upstream group called rails_servers that includes all three web containers. It also sets up a server that listens on port 80 and forwards requests to the upstream group.

Now I built docker images and start the container using
docker-compose build
docker-compose up

We can now access the Rails application through Nginx by visiting http://localhost:8080 in our web browser.Nginx 
      
 
