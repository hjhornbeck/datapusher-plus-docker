#############################
### Build DataPusher Plus ###
#############################
FROM ubuntu:jammy


LABEL maintainer="Minhaj"
LABEL org.opencontainers.image.source https://github.com/ctrepka/docker-datapusher-plus




# Set timezone
ENV TZ=UTC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone


# Set Locale
ENV LC_ALL=en_US.UTF-8


ENV SRC_DIR=/srv/app/src
ENV VENV=/usr/lib/ckan/dpplus_venv
ENV CFG_DIR=/etc/ckan/datapusher


WORKDIR ${SRC_DIR}


# Set the locale         
RUN apt-get update
RUN apt-get install --no-install-recommends -y locales
RUN sed -i "/$LC_ALL/s/^# //g" /etc/locale.gen
RUN dpkg-reconfigure --frontend=noninteractive locales
RUN update-locale LANG=${LC_ALL}


# Instal apt-utils
RUN apt-get install --no-install-recommends -y \
   apt-utils \
   python3-venv \
   python3-dev \
   python3 \
   build-essential \
   libxslt1-dev \
   libxml2-dev \
   libffi-dev \
   wget \
   curl \
   unzip \
   python3-pip \
   git\
   libpq-dev\
   file


#install qsv;
RUN wget https://github.com/jqnatividad/qsv/releases/download/0.107.0/qsv-0.107.0-x86_64-unknown-linux-gnu.zip
RUN unzip qsv-0.107.0-x86_64-unknown-linux-gnu.zip
RUN rm qsv-0.107.0-x86_64-unknown-linux-gnu.zip
RUN mv qsv* /usr/local/bin


#python env setup; link python3 to python cmd; make venv; install uwsgi;
RUN ln -s /usr/bin/python3 /usr/bin/python
RUN python -m venv ${VENV}
RUN ${VENV}/bin/pip install uwsgi


#install tests 
#Add custom values before installing tests https://github.com/dathere/testing-datapusher-plus.git
#and unable the script in startup.sh
#RUN git clone --branch master https://github.com/dathere/testing-datapusher-plus.git
#RUN ${VENV}/bin/pip install -r testing-datapusher-plus/requirements.txt
#RUN cd ..


#INSTALL DATAPUSHER-PLUS FROM SOURCE REPO
RUN git clone --branch 0.13.2 https://github.com/datHere/datapusher-plus
RUN cd ${SRC_DIR}/datapusher-plus && \
   ${VENV}/bin/pip install -r requirements-dev.txt && \
   ${VENV}/bin/pip install -e .


#SETUP CONFIG/SETTINGS.PY
RUN mkdir -p ${CFG_DIR}
COPY ./example.env ${CFG_DIR}/.env
ENV JOB_CONFIG=${CFG_DIR}/.env




RUN curl https://raw.githubusercontent.com/dathere/datapusher-plus/0.13.2/deployment/datapusher-uwsgi.ini -o ${CFG_DIR}/uwsgi.ini




COPY ./entrypoint/startup.sh /startup.sh
RUN chmod +x /startup.sh




ENTRYPOINT [ "bash", "-c", "/startup.sh" ]




EXPOSE 8800