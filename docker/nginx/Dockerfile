# Dev image
FROM nginx:1.19.2-alpine as dev

# Copy nginx config
COPY ./docker/nginx/default.conf /etc/nginx/conf.d/default.conf

# Prod image
FROM dev as prod

# Copy assets
COPY ./assets /app/public
