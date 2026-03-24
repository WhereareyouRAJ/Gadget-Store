# Stage 1: Build Stage
FROM node:20-alpine AS build

WORKDIR /app

COPY package.json ./

RUN yarn install --production         
    
# Stage 2: Production build
FROM node:20-alpine AS modules
WORKDIR /app
COPY package*.json ./

RUN yarn ci --production

# Stage 3: Final Stage
FROM gcr.io/distroless/nodejs20-debian12

COPY --from=modules /app/node_modules ./node_modules
COPY . .

USER abc:abc

EXPOSE 8080

ENTRYPOINT ["server,js"]
