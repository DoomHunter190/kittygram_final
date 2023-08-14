FROM node:18
WORKDIR /app

COPY package*.json ./

RUN npm ci && npm run build

RUN npm install --global http-server

CMD ["npx", "-y", "http-server", "-p", "9000", "/app/build"]
