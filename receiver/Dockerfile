FROM alpine:latest

RUN apk update && apk upgrade

RUN apk add postfix mailx rsyslog tcpdump python && rm -rf /var/cache/apk/*

RUN mkdir -p /etc/skel/Maildir/{new,cur,tmp}
RUN chmod -R 777 /etc/skel/Maildir/

# SPF
WORKDIR /usr/local/src
ADD https://pypi.python.org/packages/source/p/pydns/pydns-2.3.6.tar.gz /usr/local/src/
RUN tar -zxvf pydns-2.3.6.tar.gz 
RUN rm pydns-2.3.6.tar.gz 
WORKDIR /usr/local/src/pydns-2.3.6
RUN python setup.py install

WORKDIR /usr/local/src
ADD https://pypi.python.org/packages/source/p/pyspf/pyspf-2.0.9.tar.gz /usr/local/src/
RUN tar -zxvf pyspf-2.0.9.tar.gz 
RUN rm pyspf-2.0.9.tar.gz 
WORKDIR /usr/local/src/pyspf-2.0.9
RUN python setup.py install

WORKDIR /usr/local/src
ADD https://launchpad.net/pypolicyd-spf/1.3/1.3/+download/pypolicyd-spf-1.3.tar.gz /usr/local/src/
RUN tar -zxvf pypolicyd-spf-1.3.tar.gz 
RUN rm pypolicyd-spf-1.3.tar.gz  
WORKDIR /usr/local/src/pypolicyd-spf-1.3
RUN python setup.py install

WORKDIR /usr/local/src
ADD https://pypi.python.org/packages/9d/a7/1b39a16cb90dfe491f57e1cab3103a15d4e8dd9a150872744f531b1106c1/ipaddr-2.2.0.tar.gz#md5=f88353e40dec06410acfa075b8209b27 /usr/local/src/
RUN tar -zxvf ipaddr-2.2.0.tar.gz
RUN rm ipaddr-2.2.0.tar.gz
WORKDIR /usr/local/src/ipaddr-2.2.0
RUN python setup.py install

# configs
ADD main.cf /etc/postfix/main.cf
ADD master.cf /etc/postfix/master.cf
ADD policyd-spf.conf.commented /etc/python-policyd-spf/policyd-spf.conf

RUN newaliases

RUN adduser -S tester

# Directory where mail addressed to root is created
WORKDIR /home/tester/Maildir

EXPOSE 25

# It will not create a maillog unless you start rsyncd
# Since there is no way to execute postfix with forwagound, let's output maillog with the tail command so that docker will not stop
CMD ["sh", "-c", "rsyslogd; postfix start; tail -F /var/log/maillog;"]

