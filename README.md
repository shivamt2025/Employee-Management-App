 docker network create my_network
 
 docker run -d --name db_container --network my_network -e MYSQL_ROOT_PASSWORD=my_password mysql
 
 
 
changed dockerfie to -

# Use Ruby 3.1.3 as the base image
FROM ruby:3.1.3

# Set the working directory to /app
WORKDIR /app

# Copy the Gemfile and Gemfile.lock into the container
COPY Gemfile Gemfile.lock ./

# Install dependencies
RUN bundle install

# Copy the rest of the application code into the container
COPY . .

# Expose port 8080 to the outside world
EXPOSE 8080

# Start the Rails server
CMD ["rails", "server", "-b", "0.0.0.0", "-p", "8080"]
 
 
 then docker-compose.yml
 
 version: '3'
services:
  web:
    build: .
    ports:
      - "8080:8080" # Expose port 8080 on the host machine
    depends_on:
      - db # Declare dependency on the db service
    environment:
      - DATABASE_HOST=db_container # Set database host to the hostname or container name of the db service
      - DATABASE_PASSWORD=<%= ENV["SRC_DATABASE_PASSWORD"] %> # Set database password from environment variable
    networks:
      - my_network # Connect to my_network

  db:
    image: mysql
    volumes:
      - db-data:/var/lib/mysql # Mount volume for persistent data
    environment:
      - MYSQL_ALLOW_EMPTY_PASSWORD=true # Set empty password for convenience
    networks:
      - my_network # Connect to my_network

networks:
  my_network: # Define my_network for internal communication

volumes:
  db-data: # Define named volume for database data
  
  
  
Also changed the host in database.yml
 host: db_container
 
 finally,
 docker run -d --name app_container1 -p 8080:8080 --network my_network docker_on_rails  
