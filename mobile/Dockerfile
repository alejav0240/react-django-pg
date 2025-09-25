FROM node:22-alpine

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm install -g expo-cli && npm install

COPY . .

EXPOSE 19006 8081
