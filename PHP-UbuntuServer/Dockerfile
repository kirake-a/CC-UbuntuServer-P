FROM php:8.2-cli

RUN mkdir -p /usr/src/myapp

COPY index.php /usr/src/myapp

WORKDIR /usr/src/myapp

CMD [ "php", "./index.php" ]