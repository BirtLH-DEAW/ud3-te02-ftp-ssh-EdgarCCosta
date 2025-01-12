# Utilizamos una imagen oficial de Ubuntu
FROM ubuntu:latest

# Información sobre la imagen
LABEL version="1.0" \
    description="Ubuntu + Apache2 + ProFTPD + SSH + Virtual Hosts" \
    maintainer="usuarioBIRT <usuarioBIRT@birt.eus>"

# Actualizamos la lista de paquetes e instalamos los servicios necesarios
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y nano apache2 proftpd openssh-server git && \
    rm -rf /var/lib/apt/lists/*

# Creamos directorios para los sitios web
RUN mkdir -p /var/www/html/sitioprimero /var/www/html/sitiosegundo /var/www/html/ftp-anonimo

# Copiamos archivos al contenedor
COPY /ftp/indexprimero.html /var/www/html/sitioprimero/index.html
COPY /apache/sitioprimero.conf /etc/apache2/sites-available/sitioprimero.conf
COPY /ftp/proftpd.conf /etc/proftpd/proftpd.conf
COPY /ftp/sitioprimeroftp.key /etc/ssl/private/sitioprimeroftp.key
COPY /ftp/sitioprimeroftp.pem /etc/ssl/certs/sitioprimeroftp.pem
COPY /ssh/indexsegundo.html /var/www/html/sitiosegundo/index.html
COPY /apache/sitiosegundo.conf /etc/apache2/sites-available/sitiosegundo.conf
COPY /ftp/ftpusers /etc/ftpusers
COPY /ssh/sshd_config /etc/ssh/sshd_config
COPY /ftp/id_rsa /root/.ssh/id_rsa

# Habilitamos los sitios y el módulo SSL
RUN a2ensite sitioprimero.conf && \
    a2ensite sitiosegundo.conf && \
    a2enmod ssl

# Cambiamos permisos de los archivos SSL
RUN chmod 0600 /etc/ssl/private/sitioprimeroftp.key && \
    chmod 0640 /etc/ssl/certs/sitioprimeroftp.pem
# Cambiamos permisos
RUN chmod 600 /root/.ssh/id_rsa && \
    ssh-keyscan github.com >> /root/.ssh/known_hosts

# Creamos usuario edgarrodrigues1 y configuramos permisos
RUN useradd -d /var/www/html/sitioprimero -s /bin/false edgarrodrigues1 && \
    echo "edgarrodrigues1:deaw" | chpasswd && \
    chown -R edgarrodrigues1 /var/www/html/sitioprimero && \
    chmod -R 775 /var/www/html/sitioprimero

# Creamos usuario edgarrodrigues2 y configuramos permisos
RUN useradd -d /var/www/html/sitiosegundo -s /bin/bash edgarrodrigues2 && \
    echo "edgarrodrigues2:deaw" | chpasswd && \
    echo "edgarrodrigues2" >> /etc/ftpusers && \
    chown -R edgarrodrigues2 /var/www/html/sitiosegundo && \
    chmod -R 775 /var/www/html/sitiosegundo

# Clonamos el repositorio de GitHub para acceso anónimo
RUN git clone git@github.com:deaw-birt/UD3-ftp_anonimo.git /var/www/html/ftp_anonimo    

# Exponemos los puertos necesarios
EXPOSE 21 80 443 2222 60000-60100
