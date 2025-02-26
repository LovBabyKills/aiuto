#immagine
FROM rockylinux:8

#variabili d'ambiente ENV
#forse posso essere gestite in un file .env
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8



RUN dnf -y update && \
    dnf -y groupinstall "Development Tools" && \
    dnf -y module enable php:7.4 && \
    dnf -y install \
    httpd \
    php \
    php-mbstring \
    php-xml \
    php-mysqlnd \
    php-fpm \
    php-json \
    php-opcache \
    php-soap \
    php-intl \
    php-pecl-apcu \
    php-cli \
    php-gd \
    php-zip \
    php-curl && \
    dnf clean all

# Installa Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Configura Apache
#RUN ln -s /etc/httpd/conf.d /etc/httpd/conf-enabled && \
    #crea un collegamento tra i file di configurazione di apache etc/httpd/conf.d
    #pero rockylinux usa gia' etc/httpd/conf.d come directory per configurazione

RUN echo "ServerName localhost" >> /etc/httpd/conf/httpd.conf
#aggiunge servername localhost nel file di configurazione
#per evitare warning se non e' configurato durante l'avvio

# Naviga nella cartella del progetto
#docker eseguira i comandi all'interno di questa cartella
WORKDIR /var/www/html

# Copia i file di laravel nel container
#COPY . /var/www/html/
# Copia il contenuto del progetto Laravel
COPY laravelapp /var/www/html

# Copia il file di configurazione di Apache per Laravel
COPY laravel.conf /etc/httpd/conf.d/laravel.conf

# Installa le dipendenze di Composer
#RUN composer install

# Ottimizza l'autoload per la produzione
#RUN composer dump-autoload --optimize

#installa la dipendeze di laravel???
RUN composer install --no-dev --optimize-autoloader

# Imposta i permessi per i file Laravel
#change owner in modo ricorsivo -r
#dando i permessi ad apache per le cartelle
RUN chown -R apache:apache /var/www/html && \
#changemode imposta i permessi a 755 che sarebbe 
    #7 (lettura+scrittura+esecuzione) per il proprietario
    #5 (lettura+esecuzione) per il gruppo
    #5 (lettura+esecuzione) per tutti gli altri
        #laravel scrive in storage per salvare log cache etc...
        #e in cache usata da laravel per memorizza file di cache generati automaticamente
    chmod -R 755 /var/www/html/storage && \
    chmod -R 755 /var/www/html/bootstrap/cache

#verifica che apache si configurato correttamente
RUN httpd -t


# Abilita e avvia Apache
EXPOSE 80

#starta apache
#CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
CMD ["httpd", "-D", "FOREGROUND"]
#CMD ["/bin/bash"]