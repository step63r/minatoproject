# Designed to be run as 
# 
# docker run -it -p 8000:8000 jupyterhub/oauthenticator

FROM jupyterhub/jupyterhub

MAINTAINER Project Jupyter <ipython-dev@scipy.org>

RUN apt-get update -y
RUN apt-get install -y build-essential

# Install oauthenticator from git
RUN python3 -m pip install oauthenticator

# Create oauthenticator directory and put necessary files in it
RUN mkdir /srv/oauthenticator
WORKDIR /srv/oauthenticator
ENV OAUTHENTICATOR_DIR /srv/oauthenticator
ADD jupyterhub_config.py jupyterhub_config.py
ADD userlist /srv/oauthenticator/userlist
ADD ssl /srv/oauthenticator/ssl

RUN python3 -m pip install --upgrade notebook
RUN python3 -m pip install pandas matplotlib scipy seaborn scikit-learn scikit-image cython bokeh sqlalchemy beautifulsoup4 tensorflow
RUN python3 -m pip install pep8 flake8 pylint mysql-connector-python PyMySQL python-memcached pymongo happybase neo4j-driver

RUN chmod 700 /srv/oauthenticator
