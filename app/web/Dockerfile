FROM almalinux:9                                     
LABEL maintainer="Jose Moreno <jose.moreno@microsoft.com>"

# Update system and install Apache + PHP + debugging tools
RUN dnf -y update && \
    dnf -y install --allowerasing \
      httpd \
      php \
      php-fpm \
      php-json \
      php-devel \
      curl \
      wget && \
    dnf clean all

# Create runtime directory for php-fpm
RUN mkdir -p /run/php-fpm

# Redirect Apache logs to stdout/stderr
RUN ln -sf /proc/self/fd/1 /var/log/httpd/access_log && \
    ln -sf /proc/self/fd/2 /var/log/httpd/error_log

# Expose environment variables to PHP-FPM workers
RUN sed -i 's|;*clear_env\s*=.*|clear_env = no|' /etc/php-fpm.d/www.conf

# Copy web assets
COPY index.php info.php styles.css favicon.ico /var/www/html/

# Create simple health endpoints
RUN printf '{"health":"OK"}' > /var/www/html/healthcheck.html && \
    printf '<?php phpinfo(); ?>' > /var/www/html/healthcheck.php

# Expose HTTP port
EXPOSE 80

# Define a basic HTTP healthcheck
HEALTHCHECK --interval=30s --timeout=5s \
  CMD curl -f http://localhost/healthcheck.html || exit 1

# Start PHP-FPM and Apache in foreground
CMD ["sh", "-c", "/usr/sbin/php-fpm --nodaemonize & exec /usr/sbin/httpd -DFOREGROUND"]
