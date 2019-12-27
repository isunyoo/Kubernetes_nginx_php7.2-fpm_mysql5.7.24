FROM debian:stretch
ENV NPM_CONFIG_LOGLEVEL warn

ENV DEBIAN_FRONTEND noninteractive

EXPOSE 9050
  
# `apt-utils` squelches a configuration warning
# `gnupg2` is required for adding the `apt` key
RUN apt-get update
RUN apt-get -y install apt-utils gnupg2 wget

#
# Here's where the `tor` stuff gets baked into the container
#
# Keys and repository stuff accurate as of 2017-10-25
# See: https://www.torproject.org/docs/debian.html.en#ubuntu
RUN echo "deb http://deb.torproject.org/torproject.org stretch main" | tee -a /etc/apt/sources.list.d/torproject.list
RUN echo "deb-src http://deb.torproject.org/torproject.org stretch main" | tee -a /etc/apt/sources.list.d/torproject.list
#RUN gpg --keyserver keys.gnupg.net --recv A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89
RUN wget -q -O - http://deb.torproject.org/torproject.org/A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89.asc | apt-key add -
#RUN gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | apt-key add -
RUN apt-get update
RUN apt-get -y upgrade
RUN apt-get -y install tor deb.torproject.org-keyring

# The debian image does not create a default user
RUN useradd -m tor
USER tor

# Run the Tor service
CMD /usr/bin/tor -f /etc/tor/torrc