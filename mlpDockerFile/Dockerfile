FROM alexcheng/magento2

LABEL maintainer="miguelbarreiro85@gmail.com"
LABEL version="0.0.1"
LABEL description="MLP BARREIRO LDA"

ENV INSTALL_DIR /var/www/html

COPY ./disable-module /usr/local/bin/disable-module
RUN chmod +x /usr/local/bin/disable-module

RUN apt-get update && apt-get install -y ftp

COPY ./sorefoz.sh /usr/local/bin/sorefoz.sh
RUN chmod +x /usr/local/bin/sorefoz.sh

COPY ./sorefoz_cron /etc/cron.d/sorefoz_cron
RUN chmod 0644 /etc/cron.d/sorefoz_cron

COPY ./sorefoz_conv.sh /usr/local/bin/sorefoz_conv.sh
RUN chmod +x /usr/local/bin/sorefoz_conv.sh

RUN touch /var/log/sorefoz_cron.log

WORKDIR $INSTALL_DIR

