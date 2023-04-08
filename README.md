To enable persistence for the DB data and Nginx config files, we can use Docker volumes.
For the database data, I created a named volume in your docker-compose.yml file like this:
version: '3'



services:
  db:
    image: mysql
    volumes:
      - db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
  
  rails:
   build: .   
  nginx:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - rails
    volumes:
      - nginx_config:/etc/nginx/nginx_config.d
volumes:
  db_data:
  nginx_config:



then i composed it using docker-compose up -d
This will start the containers in the background (-d flag) and ensure that the named volumes are created and used for data persistence.
further you can cross check that your containers are running and using the named volumes, you can run the following command:

docker-compose ps

