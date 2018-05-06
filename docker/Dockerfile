FROM alpine:3.7

LABEL maintainer="mike-sino"
LABEL version="1.0"

# Install GUN libc
ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

RUN ALPINE_GLIBC_BASE_URL="https://github.com/sgerrand/alpine-pkg-glibc/releases/download" && \
    ALPINE_GLIBC_PACKAGE_VERSION="2.27-r0" && \
    ALPINE_GLIBC_BASE_PACKAGE_FILENAME="glibc-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_BIN_PACKAGE_FILENAME="glibc-bin-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_I18N_PACKAGE_FILENAME="glibc-i18n-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    apk add --no-cache --virtual=.build-dependencies wget ca-certificates bash && \
    wget "https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub" \
        -O "/etc/apk/keys/sgerrand.rsa.pub" && \
    wget "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
         "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
         "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    apk add --no-cache \
        "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    rm "/etc/apk/keys/sgerrand.rsa.pub" && \
    /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 "$LANG" || true && \
    echo "export LANG=$LANG" > /etc/profile.d/locale.sh && \
    apk del glibc-i18n && \
    rm "/root/.wget-hsts" && \
    apk del --purge .build-dependencies && \
    rm  "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    apk add --no-cache --virtual=.build-dependencies bash

# Configure Miniconda
ENV CONDA_DIR /opt/conda
ENV PATH $CONDA_DIR/bin:$PATH
ENV MINICONDA_VER latest
ENV MINICONDA Miniconda3-$MINICONDA_VER-Linux-x86_64.sh
ENV MINICONDA_URL https://repo.continuum.io/miniconda/$MINICONDA

# Install Miniconda
RUN cd /tmp && \
    mkdir -p $CONDA_DIR && \
    wget $MINICONDA_URL -O miniconda.sh && \
    bash miniconda.sh -f -b -p $CONDA_DIR && \
    rm miniconda.sh && \
    $CONDA_DIR/bin/conda update -y conda

RUN conda upgrade -y pip && \
    rm -r "$CONDA_DIR/pkgs/" && \
    conda clean --all

# Install Jupyter
RUN conda install jupyter && \
    mkdir /notebook/ && \
    jupyter notebook --generate-config
COPY config/jupyter_notebook_config.py /root/.jupyter/jupyter_notebook_config.py
RUN conda install -c anaconda-nb-extensions nb_conda_kernels && \ 
    conda create -n python2 python=2.7 ipykernel && \
    conda create -n python3 python=3.6 ipykernel && \
    python -m nb_conda_kernels.install --enable --prefix="%CONDA_PREFIX"

# Install JupyterLab
RUN conda install -c conda-forge JupyterLab
RUN jupyter serverextension enable --py jupyterlab --sys-prefix


# Install Python for MySQL
RUN conda install pymysql && \ 
    conda install pandas && \ 
    conda install matplotlib && \ 
    pip install ipython-sql

EXPOSE 8080 8888
CMD [ "/sbin/init" ]
