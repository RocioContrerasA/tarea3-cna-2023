# syntax=docker/dockerfile:1

FROM node:18-alpine

WORKDIR /usr/app

COPY package.json .

RUN npm install --quiet

COPY ./ ./
