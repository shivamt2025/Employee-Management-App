Firstly I updated nginx.config file as follows-:
events {}

http {
  # Define the rate limit zone with a limit of 10 requests per second and a burst of 20 requests
  limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

  upstream rails {
    server rails:3000;
  }

  server {
    listen 8080;

    location / {
      # Apply the rate limit to all requests
      limit_req zone=mylimit burst=20;

      # Proxy requests to the upstream Rails server
      proxy_pass http://rails;

      # Set headers to pass along the client's IP address and the original protocol
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}

This updated configuration file adds the limit_req_zone directive to define a rate limit zone named "mylimit". The zone is set to store data in memory (using $binary_remote_addr) with a size of 10 megabytes, and limit the rate of requests to 10 requests per second. The burst parameter is set to 20, which allows for a burst of up to 20 requests before the limit takes effect.
The limit_req directive is then added to the location block to apply the rate limit to all requests. Finally, the proxy_pass and proxy_set_header directives are used to proxy requests to the upstream Rails server and pass along the necessary headers for IP address and protocol information.

Then reloaded the running docker container using
docker exec <container-name> nginx -s reload

After running this command, the new rate limit should be in effect for all requests in this  application
