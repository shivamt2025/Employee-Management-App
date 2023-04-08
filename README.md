After cloning the given repo download all the dependencies and then make a Dockerfile to run the container
I created a Dockerfile as follows
FROM ruby:3.1.3 

# Set the working directory to /app
WORKDIR /app

# Copy the Gemfile and Gemfile.lock into the container
COPY Gemfile Gemfile.lock ./

# Install dependencies
RUN bundle install

# Copy the rest of the application code into the container
COPY . .

# Expose port 3000 to the outside world
EXPOSE 3000

# Start the Rails server
CMD ["rails", "server", "-b", "0.0.0.0"]

Then i built the image named rails_on_docker
and finally ran the container using
run -dp 3000:3000 rails_on_docker
