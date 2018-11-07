# specify base image
FROM node:alpine
## install some dependencies
WORKDIR /app
COPY ./package.json .
RUN npm install
COPY . .
# default command
CMD ["npm","start"]