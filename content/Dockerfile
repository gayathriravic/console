# Copying .env in 17 line is a temporary solution for issue with babel-loader package in react-scripts 

FROM node:10.11.0-alpine as ui-generator

WORKDIR /app

# Copy licenses
COPY licenses/ /app/licenses/

# Install global dependencies
RUN apk update && apk upgrade && \
    apk add --no-cache curl

# Install root and app dependencies
COPY ./temp /app
RUN npm run bootstrap:prod
COPY package.json package-lock.json config-overrides.js .env /app/content/
RUN cd /app/content && npm install --no-optional

# Copy sources
COPY ./src /app/content/src
COPY ./public /app/content/public

# Set env variables 
ENV PRODUCTION true
ENV CI true

# Test & Build
RUN cd /app/content && npm test
RUN cd /app/content && npm run build

FROM nginx:1.14.0-alpine
LABEL source git@github.com:kyma-project/console.git

COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=ui-generator /app/content/build var/public
COPY --from=ui-generator /app/licenses/ /app/licenses/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]