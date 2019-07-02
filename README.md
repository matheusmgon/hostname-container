# HOSTNAME PARA CONTAINER
                                            
                                            
                                            
                                            

Este projeto serve para facilitar o acesso a containers, tanto para fins internos como produção

Para ser feita esta configuração, será necessário:

- apache2 ( no host que possuir os containers);
- mods do apache: proxy, proxy_http rewrite;
- docker;

Inicilamente vamos criar um container e definir uma porta para o mesmo:


	sudo docker run -dti -v $HOME:/home/shared -p 8080:80 --name=web nginx:latest
  
  ![](https://i.screenshot.net/5qmmptj)

Feito o comando a cima, verifique se o mesmo está em execução e se a porta foi corretamente compartilhada:

	
	sudo docker ps

![](https://i.screenshot.net/83rrgu7)

Agora, vamos para o Apache:


	vim /etc/apache2/sites-avaliable/site-web.conf

	<VirtualHost *:80>

        RewriteEngine On                        # Ativar o RewriteEngine
        ProxyRequests Off						# Requisições reversas no proxy
        ProxyPreserveHost On 					# Preservar o endereço no endereço de navegação
        ServerName 	web.local					# Nome do host a ser configurado
        <Proxy *>
                Require all granted
        </Proxy>
        ProxyPass "/" "http://localhost:8080/" connectiontimeout=5 timeout=30 keepalive=on
        ProxyPassReverse "/" "http://localhost:8080/"
        ErrorLog ${APACHE_LOG_DIR}/site-web-error.log
        CustomLog ${APACHE_LOG_DIR}/site-web-access.log combined
</VirtualHost>
    # vim: syntax=apache ts=4 sw=4 sts=4 sr noet

Feito a configuração do vhost, ative-o:

	cd /etc/apache2/sites-available/
	a2ensite site-web.conf
	service apache2 restart
	cd

	Como estamos usando um ambiente de teste, vamos apontar no arquivo hosts o domínio "web.local para nossa maquina"

Agora vamos ver se funcionou...


![](https://media.giphy.com/media/LmeqKc5ReYL9RfPILw/giphy.gif)



OBRIGADO
