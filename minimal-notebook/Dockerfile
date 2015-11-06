FROM alpine-miniconda

MAINTAINER CognitiveScale <devops@cognitivescale.com>

USER root

# Install all OS dependencies for fully functional notebook server
ENV DEBIAN_FRONTEND noninteractive

RUN apk update 
RUN apk add  --update-cache git  
RUN apk add vim 
RUN apk add wget 
RUN apk add python-dev 
RUN apk add ca-certificates 
RUN apk add bzip2 
RUN apk add unzip 
RUN apk add sudo


# Configure environment
ENV CONDA_DIR /data/opt/conda
ENV PATH $CONDA_DIR/bin:$PATH
ENV SHELL /bin/bash
ENV NB_USER jovyan
ENV NB_UID 1000

# Install Jupyter notebook
RUN conda install --yes \
    'notebook=4.0*' \
    terminado \
    && conda clean -yt

# Create jovyan user with UID=1000 and in the 'users' group
# Grant ownership over the conda dir and home dir, but stick the group as root.
RUN adduser -s /bin/bash -S -u $NB_UID $NB_USER 
RUN mkdir /home/$NB_USER/work 
RUN mkdir /home/$NB_USER/.jupyter 
RUN mkdir /home/$NB_USER/.local 
RUN chown -R $NB_USER:users $CONDA_DIR 
RUN chown -R $NB_USER:users /home/$NB_USER

# Configure container startup
EXPOSE 8888
WORKDIR /home/$NB_USER/work
ENTRYPOINT ["tini", "--"]
CMD ["start-notebook.sh"]

# Add local files as late as possible to avoid cache busting
COPY start-notebook.sh /usr/local/bin/
COPY jupyter_notebook_config.py /home/$NB_USER/.jupyter/
RUN chown -R $NB_USER:users /home/$NB_USER/.jupyter
