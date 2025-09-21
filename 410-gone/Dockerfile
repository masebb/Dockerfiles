FROM nginx:1.29.1

# Create HTML template with placeholders
RUN echo '<!DOCTYPE html> \
    <html lang="en"> \
    <head> \
    <meta charset="UTF-8"> \
    <title>410 Gone</title> \
    <style>body{font-family:sans-serif;text-align:center;margin-top:100px;}</style> \
    </head> \
    <body> \
    <h1>410 Gone</h1> \
    <p>$SERVICE_NAME has been discontinued</p> \
    <a href="$ORG_LINK">$ORG_NAME</a> \
    </body> \
    </html>' > /usr/share/nginx/html/410.template.html

# Create startup script using sed for variable substitution
RUN echo '#!/bin/sh' > /docker-entrypoint.sh && \
    echo 'sed -e "s/\$SERVICE_NAME/$SERVICE_NAME/g" -e "s/\$ORG_NAME/$ORG_NAME/g" -e "s|\$ORG_LINK|$ORG_LINK|g" /usr/share/nginx/html/410.template.html > /usr/share/nginx/html/410.html' >> /docker-entrypoint.sh && \
    echo 'exec nginx -g "daemon off;"' >> /docker-entrypoint.sh && \
    chmod +x /docker-entrypoint.sh


RUN echo 'server { \
    listen 80 default_server; \
    listen [::]:80 default_server; \
    server_name _; \
    location / { \
    return 410; \
    } \
    error_page 410 /410.html; \
    location = /410.html { \
    root /usr/share/nginx/html; \
    } \
    }' > /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["/docker-entrypoint.sh"]