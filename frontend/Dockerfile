# Build stage
FROM node:18 AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

RUN node --max_old_space_size=512 ./node_modules/@angular/cli/bin/ng build

# Production stage
FROM nginx:latest

COPY --from=build /app/dist/angular-15-crud /usr/share/nginx/html

EXPOSE 80