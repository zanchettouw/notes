FROM node:carbon

WORKDIR /app

RUN npm -g install serve
COPY package*.json ./

RUN npm install

COPY . /app
RUN npm run build

EXPOSE 8080
CMD ["serve", "-s", "dist", "-p", "8080"]
