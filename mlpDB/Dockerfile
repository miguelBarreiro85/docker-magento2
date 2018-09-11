FROM mysql:5.6.41

LABEL maintainer="miguelbarreiro85@gmail.com"
LABEL version="0.0.1"
LABEL description="MLP BARREIRO LDA"

COPY ./deleteProducts.sh /usr/local/bin/deleteProducts.sh
RUN chmod +x /usr/local/bin/deleteProducts.sh

COPY ./deleteCategories.sh /usr/local/bin/deleteCategories.sh
RUN chmod +x /usr/local/bin/deleteCategories.sh

COPY ./deleteCategories.sql /home/deleteCategories.sql
COPY ./deleteProducts.sql /home/deleteProducts.sql



