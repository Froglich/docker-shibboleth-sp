FROM debian:buster
MAINTAINER kim.lindgren@slu.se
ENV DOMAIN_NAME=example.com
ENV SERVICE_ADDRESS=example
ENV SERVICE_PORT=80
RUN apt-get update
RUN apt-get install -y curl gnupg openssl sudo git golang
RUN curl http://pkg.switch.ch/switchaai/SWITCHaai-swdistrib.asc | apt-key add -
RUN echo 'deb http://pkg.switch.ch/switchaai/debian buster main' | tee /etc/apt/sources.list.d/SWITCHaai.list > /dev/null
RUN apt-get update
RUN apt-get install -y --install-recommends shibboleth libapache2-mod-shib2
RUN a2enmod proxy proxy_http rewrite ssl
RUN shib-keygen -f -u _shibd -h $DOMAIN_NAME -y 7 -e https://$DOMAIN_NAME/shibboleth -o /etc/shibboleth
RUN mkdir /etc/apache2/ssl
RUN openssl req -new -nodes -subj /CN=$DOMAIN_NAME -keyout /etc/apache2/ssl/ssl.privkey > /etc/apache2/ssl/ssl.csr
RUN openssl rsa -in /etc/apache2/ssl/ssl.privkey -out /etc/apache2/ssl/ssl.key
RUN openssl x509 -in /etc/apache2/ssl/ssl.csr -out /etc/apache2/ssl/ssl.crt -req -signkey /etc/apache2/ssl/ssl.key -days 7300
RUN mkdir /etc/zeus
RUN cd /tmp && git clone https://github.com/Froglich/zeus.git
RUN cd /tmp/zeus && go build
RUN mv /tmp/zeus/zeus /usr/bin/zeus
RUN rm -rf /tmp/zeus
RUN sudo apt-get remove --auto-remove -y git golang
COPY media/tasks.json /etc/zeus/tasks.json
COPY media/shibboleth2.xml /etc/shibboleth/shibboleth2.xml
COPY media/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY media/wrapper /usr/bin/d-wrapper
RUN sed -i s/SERVICE/$SERVICE_ADDRESS/g /etc/apache2/sites-available/000-default.conf
RUN sed -i s/PORT/$SERVICE_PORT/g /etc/apache2/sites-available/000-default.conf
RUN chmod +x /usr/bin/d-wrapper
RUN cat /etc/shibboleth/shibboleth2.xml
RUN cat /etc/apache2/sites-available/000-default.conf
RUN shibd -t
RUN apache2ctl configtest
RUN service shibd stop
RUN service apache2 stop
CMD /usr/bin/d-wrapper
