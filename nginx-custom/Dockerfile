# Use the official NGINX image as the base
FROM nginx:latest

# Set maintainer information
LABEL maintainer="Divyanshu08" \
      description="Custom NGINX image for Pterodactyl" \
      version="1.0"

# Update system packages and install necessary tools
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    nano \
    && apt-get clean

# Remove default NGINX configuration
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom NGINX configuration from the repository
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Ensure correct permissions
RUN chmod 644 /etc/nginx/conf.d/default.conf

# Expose the necessary ports
EXPOSE 80 443

# Run NGINX in the foreground
CMD ["nginx", "-g", "daemon off;"]
