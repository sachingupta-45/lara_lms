FROM nginx:alpine

# Copy our custom Nginx configuration
COPY docker/nginx/default.conf /etc/nginx/conf.d/default.conf

# Copy Laravel public files into the Nginx container
COPY public/ /var/www/html/public/
