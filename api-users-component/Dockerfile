# Use NodeJS base image
FROM node:13

# Create app directory in Docker
WORKDIR /usr/src/app

# Install app dependencies by coping
# package.json and package-lock.json
COPY package*.json ./

# Install dependencies in Docker
RUN npm install --force --loglevel verbose

# Copy app from local env into the created Docker Image
COPY . .

# Set the API's port number
EXPOSE 8080
ENV NODE_OPTIONS=--max_old_space_size=4096

#Define Docker's behavior when the image is run
CMD ["npm", "run", "prod", "|", "tee", "errorlog.txt"]

# RUN node ./www/server.js
