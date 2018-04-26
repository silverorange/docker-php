FROM silverorange/php-app:base
LABEL maintainer="Michael Gauthier <mike@silverorange.com>"

# Configure application
RUN mkdir -p /so/app/www
RUN mkdir -p /so/phpsessions/app
RUN mkdir -p /so/phpsessions/appadmin
RUN . /etc/apache2/envvars && chown -R "$APACHE_RUN_USER:$APACHE_RUN_GROUP" /so
WORKDIR /so/app
RUN rmdir /var/www/html
RUN ln -sf /so/app/www /var/www/html
COPY app.conf /etc/apache2/sites-available/000-default.conf
