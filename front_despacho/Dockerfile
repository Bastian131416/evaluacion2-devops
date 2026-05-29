FROM node:20-alpine AS build
WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
WORKDIR /usr/share/nginx/html

RUN rm -rf ./*
COPY --from=build /app/dist .

RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chmod -R 755 /usr/share/nginx/html && \
    ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log

RUN sed -i 's/listen       80;/listen       8080;/g' /etc/nginx/conf.d/default.conf && \
    sed -i '/user  nginx;/d' /etc/nginx/nginx.conf && \
    pidfile_old="pid        \/var\/run\/nginx.pid;" && \
    pidfile_new="pid        \/tmp\/nginx.pid;" && \
    sed -i "s|$pidfile_old|$pidfile_new|g" /etc/nginx/nginx.conf

USER nginx

EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
