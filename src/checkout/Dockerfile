# Stage 1: Build Stage
FROM node:20-alpine AS build

WORKDIR /app

COPY .yarnrc.yml ./
COPY .yarn/ ./.yarn
COPY package.json yarn.lock ./

RUN corepack enable && corepack prepare yarn@4.11.0 --activate

RUN yarn install          
    
# Stage 2: Production build
FROM node:20-alpine AS modules
WORKDIR /app
COPY package*.json ./

RUN corepack enable && corepack prepare yarn@4.11.0 --activate

RUN yarn config set nodeLinker node-modules


RUN yarn install 
RUN ls -la /app/node_modules

# Stage 3: Final Stage
FROM gcr.io/distroless/nodejs20-debian12
WORKDIR /app


COPY --from=modules /app/node_modules /app/node_modules 
COPY . .

USER 65532:65532

EXPOSE 8080   

CMD ["server.js"]
