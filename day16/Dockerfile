# Use official nginx image
FROM nginx:alpine
# Remove default nginx.conf
RUN rm /etc/nginx/conf.d/default.conf
# Copy custom nginx config (we will define HTTPS server)
COPY nginx.conf /etc/nginx/nginx.conf
# Copy SSL certificates into container
COPY certs/server.crt /etc/ssl/certs/server.crt
COPY certs/server.key /etc/ssl/private/server.key
# Expose HTTPS port
EXPOSE 443
# Start nginx
CMD ["nginx", "-g", "daemon off;"]
