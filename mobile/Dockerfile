FROM node:22-alpine

WORKDIR /app

RUN npm install -g @expo/ngrok

COPY package.json package-lock.json ./
RUN npm install -g expo-cli && npm install

COPY . .

EXPOSE 19000 19001 19002 19003 19006 8081

# Comando para iniciar Expo en modo túnel
CMD ["npx", "expo", "start", "--tunnel"]
