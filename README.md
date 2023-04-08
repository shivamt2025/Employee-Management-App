To launch an Nginx container and configure it as a reverse proxy to the Rails application, I followed  these steps:
Created a new directory and added Nginx configuration file as follows
events {}
http {
  upstream rails {
    server rails:3000;
  }
  server {
    listen 8080;
    location / {
      proxy_pass http://rails;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
This configuration file sets up Nginx to listen on port 8080 and forward requests to the Rails application running on port 3000. It also sets up some headers to pass along with the request.
Now created a Dockerfile for Nginx container as follows
FROM nginx:latest
COPY nginx.config /etc/nginx/nginx.config
This Dockerfile uses the latest version of the Nginx image and copies the nginx.config file we created earlier to the appropriate directory in the container.
Now built the docker image
now run docker-compose up
both rrails and nginx container will start running

Now access the Rails application through Nginx by visiting http://localhost:8080 in your web browser. Nginx
