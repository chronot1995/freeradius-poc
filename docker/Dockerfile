FROM alpine:3.12.3
RUN apk update
RUN apk add --no-cache bash bash-doc bash-completion
RUN apk add openssl freeradius=3.0.21-r3 freeradius-eap=3.0.21-r3 freeradius-radclient
RUN rm /var/cache/apk/*

COPY ./config/authorize ./config/authorized_macs /etc/raddb/mods-config/files/
COPY ./config/clients.conf /etc/raddb/
COPY ./config/default /etc/raddb/sites-available/
COPY ./config/eap ./config/files /etc/raddb/mods-available/

# Change certificates expiration from 60 to 3652 days
RUN \
  sed -i '/default_days/c\default_days            = 3652' /etc/raddb/certs/ca.cnf && \
  sed -i '/default_days/c\default_days            = 3652' /etc/raddb/certs/server.cnf && \
  sed -i '/default_days/c\default_days            = 3652' /etc/raddb/certs/client.cnf

# Generate demo certificates
RUN cd /etc/raddb/certs && \
    ./bootstrap && \
    chown radius:radius *

# Redirect logs to STDOUT
RUN ln -sf /dev/stdout /var/log/radius/radius.log

EXPOSE \
    1812/udp \
    1813/udp

CMD ["radiusd","-xx","-f"]
#CMD ["/bin/bash"]
