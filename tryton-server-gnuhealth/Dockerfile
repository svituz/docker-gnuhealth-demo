# gnuhealth-demo 3.0

FROM debian:jessie
MAINTAINER Vittorio Meloni <vittorio.meloni@crs4.it>

ENV container docker

# Add user gnuhealth
RUN useradd gnuhealth -m -s /bin/bash -f -1
RUN echo "gnuhealth:gnuhealth" | chpasswd

# Install all the required packages
RUN apt-get update && apt-get install -y apt-utils && apt-get upgrade -y

RUN apt-get install -y --fix-missing build-essential less wget nmap netcat net-tools vim python-imaging python-dev python-setuptools libxml2-dev \
        libxslt1-dev libldap2-dev libsasl2-dev libz-dev libpq-dev libjpeg-dev python-ldap cracklib-runtime libcrack2 \
        libcrack2-dev xterm locales
RUN sed -i "s/# en_US/en_US/g" /etc/locale.gen
RUN locale-gen

RUN easy_install pip
RUN pip install cracklib hl7apy defusedxml>=0.4.1 Flask>=0.10.1 Flask-Login==0.2.11 Flask-RESTful>=0.3.2 Flask-WTF>=0.11 \
	PyWebDAV>=0.9.8 qrcode>=5.1 tornado>=4.0.2 vobject>=0.8.1rc0

RUN pip install flask-tryton>=0.3 --no-deps

ENV TERM=xterm
ENV TRYTOND_CONFIG=/etc/tryton/trytond.conf
ENV TRYTOND_PASSWORD=jkUbZGvFNeugk
ENV GNUHEALTH_DIR=/opt/

USER gnuhealth

WORKDIR /home/gnuhealth
RUN wget http://ftp.gnu.org/gnu/health/gnuhealth-3.0.8.tar.gz && tar zxvf gnuhealth-3.0.8.tar.gz

WORKDIR /home/gnuhealth/gnuhealth-3.0.8
# WORKDIR /home/gnuhealth/gnuhealth-3.0.8
RUN sed -i 's/serverpass$/#serverpass/g' gnuhealth-setup
RUN ./gnuhealth-setup install

RUN echo "export PYTHONPATH=\$PYTHONPATH:/home/gnuhealth/gnuhealth-3.0.8/backend/fhir/" >> /home/gnuhealth/.gnuhealthrc

RUN sed -i "s/TRYTON_DATABASE='gnuhealth_demo_test'/TRYTON_DATABASE='health30'/g" /home/gnuhealth/gnuhealth-3.0.8/backend/fhir/server/config.py
RUN echo "    TRYTON_CONFIG='/etc/tryton/trytond.conf'" >> /home/gnuhealth/gnuhealth-3.0.8/backend/fhir/server/config.py

USER root

COPY trytond.conf /etc/tryton/trytond.conf
COPY run_services /usr/local/bin/run_services
RUN chmod +x /usr/local/bin/run_services

VOLUME /var/lib/tryton

USER gnuhealth

WORKDIR /home/gnuhealth

CMD ["/usr/local/bin/run_services"]
#CMD ["bash"]

#ports for mllp, fhir and tryton
EXPOSE 2575 5000 8000