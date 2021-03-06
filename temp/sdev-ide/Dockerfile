FROM serialdev/sdev-emacs

RUN apt-get update && apt-get install opam -y && \
    opam init && \
    opam install utop -y && \
    opam install tuareg -y && \
    opam install merlin -y && \
    opam user-setup install  

RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - && \
    apt-get install nodejs -y && \
    npm install -g bs-platform && \
    npm install npm --global && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg |  apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && apt-get install yarn


RUN apt-get install default-jdk -y &&\
    wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein && \
    chmod +x lein && mv lein /usr/local/bin && lein -v && \
    apt-get install maven -y && \
    git clone https://github.com/jdee-emacs/jdee-server.git && \
    cd jdee-server && \
    mvn -Dmaven.test.skip=true package
    


RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean
    
RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
    wget --quiet https://repo.continuum.io/archive/Anaconda3-5.1.0-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh

ENV PATH /opt/conda/bin:$PATH


RUN conda update -n base conda && \
    conda install pytorch -c pytorch && \
    conda install virtualenv && \
    conda install torchvision && \
    conda install jupyter &&  \
    conda install pip && \
    pip install meson


RUN git clone --recursive https://github.com/Andersbakken/rtags.git && \
    cd rtags && cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 . && make && \
    export PATH=$PATH:/mnt/rtags/bin


# install rustup toolchain
RUN curl https://sh.rustup.rs -sSf | \
    sh -s -- --default-toolchain nightly -y

ENV RUSTUP_HOME=/rust
ENV CARGO_HOME=/cargo 
ENV PATH=/cargo/bin:/rust/bin:$PATH

RUN echo "(curl https://sh.rustup.rs -sSf | sh -s -- -y --default-toolchain nightly --no-modify-path) && rustup default nightly" > /install-rust.sh && chmod 755 /install-rust.sh && \
    bash install-rust.sh && \
    rustup component add rust-src && \
    cargo install racer

# Install SBCL from the tarball binaries.
RUN wget http://prdownloads.sourceforge.net/sbcl/sbcl-1.2.9-x86-64-linux-binary.tar.bz2 -O /tmp/sbcl.tar.bz2 && \
    mkdir /tmp/sbcl && \
    tar jxvf /tmp/sbcl.tar.bz2 --strip-components=1 -C /tmp/sbcl/ && \
    cd /tmp/sbcl && \
    sh install.sh && \
    cd /tmp \
    rm -rf /tmp/sbcl/

RUN cd /tmp/  && \
    wget http://beta.quicklisp.org/quicklisp.lisp && \
    echo "(defvar *dist-url* \"http://beta.quicklisp.org/dist/quicklisp/2015-12-18/distinfo.txt\") \n (load \"quicklisp.lisp\") \n (quicklisp-quickstart:install :path \"/root/quicklisp/\" :dist-url *dist-url*) \n (with-open-file (out \"/root/.sbclrc\" :direction :output) \n (format out \"(load \\"/root/quicklisp\/setup.lisp\\")\"))    (ql:quickload \"quicklisp-slime-helper\")" > /tmp/install.lisp && \
    sbcl --non-interactive --load install.lisp


#---------------------------------------------------------------------------------------------------#
#                                          Important tools                                          #
#---------------------------------------------------------------------------------------------------#

RUN apt-get install firefox -y && \
    apt-get install silversearcher-ag -y && \
    git clone https://github.com/domtronn/all-the-icons.el.git && \
    cp /all-the-icons.el/fonts/all-the-icons.ttf /usr/local/share/fonts && \ 
    cp /all-the-icons.el/fonts/file-icons.ttf /usr/local/share/fonts && \ 	
    cp /all-the-icons.el/fonts/fontawesome.ttf /usr/local/share/fonts && \ 
    cp /all-the-icons.el/fonts/material-design-icons.ttf /usr/local/share/fonts && \ 
    cp /all-the-icons.el/fonts/octicons.ttf /usr/local/share/fonts && \ 
    cp /all-the-icons.el/fonts/weathericons.ttf /usr/local/share/fonts

EXPOSE 22

CMD    ["/usr/sbin/sshd", "-D"]


# RUN git clone --recursive https://github.com/Wilfred/remacs.git 
    
#https://debbugs.gnu.org/cgi/bugreport.cgi?bug=23529


# RUN cd /remacs && rustup install `cat rust-toolchain` && \
#     ./autogen.sh && ./configure --enable-rust-debug && setarch $(uname -m) -R make

#                                         Install leiningen                                         #



# # docker run -v //C/Users/andre/Desktop/SerialDev:/mnt/working_dir --net="host" -it sdev-emacs
## docker run --rm -it --security-opt seccomp=unconfined sdev-ide


