# Deploy-de-Aplicacao
Documentação de como fazer deploy de uma aplicação

OBS: Todos os comandos que tiverem "" as aspas são somente para exemplificar, no comando não é para colocar as aspas. E se você não tiver conta no google cloud crie uma

## 1 - Acessar aba da esquerda de "Instâncias de VM" (Virtual Machine).


## 2 - Depois criar instância de VM

	2.1 - Configurar a máquina, e não esquecer de permitir no firewall o tráfego HTTP e HTTPS. Se não fizer isso não dará para fazer o deploy da aplicação.


## 3 - Colocar o ip externo em static

	3.1 - Vai nos 3 pontinhos da instância criada e seleciona a opção "ver detalhes da rede".

	3.2 - Vai na parte de "Endereços IP"

	3.3 - Seleciona a aba de "Edereços de IP Externos", cria qualquer nome e pronto


## 4 - Adicionando chave ssh para não precisar de senha toda vez que for acessar a aplicação no servidor:

	4.1 - Usar o git bash para criar a chave:
		ssh-keygen

	4.2 - Vai te pedir para colocar a senha que você quer para acessar a chave ssh.

	4.3 - Para acessar pela primeira vez a chave você terá que digitar:
		eval $(ssh-agent)

	4.4 - Adicionar a chave para que o ssh reconheça a chave do usuário:
		ssh-add ~/.ssh/"nome da chave que foi criada"
		(ex: ssh-add ~/.ssh/id_rsa)

	OBS: As etapas 4.3 e 4.4 precisam ser feitas todas vez que você reiniciar o computador

	4.5 - Abrir a pasta ssh:
		start ~/.ssh

	4.6 - E abrir o arquivo "id_rsa.pub" e copiar a chave que está nele.

	4.7 - Vá no Google Cloud, na parte de Metadados dentro de "Compute Engine" e vá na aba de "Chaves SSH" e adicione a chave. Dessa forma o usuário que você criou a chave irá ter acesso ao servidor

	4.8 - Acessando o servidor:
		4.8.1 - Copie a o ip externo que você criou da sua máquina virtual e utilize o comando:
			ssh "ip do servidor"

			OBS: Se eu quiser mandar meu usuário também eu faço assim:
				ssh "nome do usuario"@"ip do servidor"

			- Ele vai perguntar se você confia no ip ai você digita "yes"

		4.8.2 -  Agora você já está no servidor da google. Nesse caso irá aparecer o nome do usuário e o nome do usuário do servidor

		4.8.3 - Já no servidor você irá atualizar os pacotes do sistema operacional usando esses 2 comandos:
			sudo apt update
			sudo apt upgrade

		4.8.4 - Se você quiser reiniciar o servidor é só usar o comando:
			sudo reboot


    OBS: Toda vez que eu reiniciar o meu pc eu tenho que usar esses comandos para acessar o meu servidor do google:
      eval $(ssh-agent)

      ssh-add ~/.ssh/"
      (ex: ssh-add ~/.ssh/id_rsa)

      ssh "ip do servidor"
      (ex: ssh 34.151.251.8)


## 5 - Usando o git para para fazer as alterações do projeto no servidor:

	5.1 - Adicionando o projeto no git:

		5.1.1 - Abrir o git bash na pasta do projeto

		5.1.2 - Configurar o email e usuário:
			git config --global user.email "joaozinho@gmail.com"
			git config --global user.name "Joaozinho"

		5.1.3 - Inicializar o git na pasta:
			git init

		5.1.4 - Criar o .gitignore:
			nano .gitignore
		
		5.1.5 - Adicionar todos os arquivos no git:
			git add .
			
			OBS: para verificar os arquivos que estão na pasta é so digitar o comando:
				ls
				ls -la
		
		5.1.6 - Comitar o projeto:
			git commit -am 'descrição'
	
	5.2 - Criando repositório no servidor:

		5.2.1 - Abra outro git bash (para acessar o servidor por ele, lembrar das etapas que tem qeu fazer se você reiniciar o pc)

		5.2.2 - Criar as pastas do projeto dentro do servidor(Uma cópia e a definitiva):
			mkdir nome-da-pasta-copia
			mkdir nome-da-pasta

			OBS: O comando ls verifica as pastas que tem no local

		5.2.3 - Entrar na pasta que foi criada:
			cd nome-da-pasta-copia/
		
		5.2.4 - Criar um repositório na pasta cópia:
			git init --bare

		5.2.5 - Voltar para pasta anterior:
			cd ..

		5.2.6 - Vai na pasta principal agora:
			cd nome-da-pasta

		5.2.7 - Inicia o git nela:
			git init
		
		5.2.8 - Adcionar um repositório remoto (você pode fazer isso com o git hub, porém eu fiz com uma cópia no próprio servidor):
			git remote add nome-do-repositório caminho-da-pasta-copia

			OBS: Se não souber o nome do usuário é so quando estiver colocando o caminha /home/, apertar tab que ele mostra os usuários
	
	5.3 - Adcionando o repositório criado no servidor para o nosso projeto no nosso computador:

		5.3.1 - Adicionando um repositório remoto do projeto que está no meu computador com o do servidor:
			git remote add nome-do-repositorio-pincipal-do-servidor ip-do-servidor:nome-do-repositorio-copia
			(EX: git remote add agenda 34.247.214.145:repo-agenda)
		
		5.3.2 - Fazendo push do repositório do nosso computador para o do servidor:
			git push nome-da-pasta-principal master
			(EX: git push agenda master)

		5.3.3 - Fazendo o pull no repositório do nosso servidor (Vai na pasta principal do servidor e digita o comando):
			git pull nome-do-repo-principal master
			(EX: git pull agenda master)
		
		Dica: Agora qualquer alteração que você fizer no projeto do seu computador irá abrir a pasta com o bash e usar o seguintes comandos:
			1 - Adicionar os arquivos:
				git add .

			2 - Commitar as modificações que eu fiz
				git commit -am 'descrição'

			3 - Mandar as alterações para o repositório cópia do servidor
				git push nome-repo-principal-servidor master
				(EX: push agenda master)
			
			4 - Entrar na pasta principal do servidor (EX: cd agenda) e puxar da cópia para pricipal:
				git pull nome-repo-principal master
				(EX: git pull agenda master)


## 6 - Instalando o Node e o Pm2 no servidor:
	6.1 - Entre no servidor

	6.2 - Digite os comando para instalar o curl e node:
		sudo apt install curl -y

		curl -sL https://deb.nodesource.com/setup_18.x | sudo bash -

		sudo apt install nodejs -y

	6.3 - Vai na pasta da aplicação principal e instala o npm:
		npm i

		OBS: Você pode dar um ls de novo para verificar se a pasta "node_modules" foi instalada

	6.4 - Instalar um pacote do node globalmente Pm2 (ele que vai gerenciar a nossa aplicação e outras também, se a quisermos ter mais de uma aplicação no mesmo servidor):
		sudo npm i -g pm2

	6.3 - Subindo o processo do aplicativo utilizando o pm2:
		pm2 start home/nome-do-usuario/nome-da-pasta-da-aplicação/server.js/ --name nome-que-você-quer-dar
		(EX: pm2 start /home/Bruno/agenda/server.js -name Agenda)

		OBS: Verificar sempre a porta que você está usando no "server.js", pois se você colocar mais de um projeto e as portas forem a mesma vai dar conflito, para aditar isso é so dar um:
			nano server.js

	6.4 - Criar o arquivo ".env", e colocar a variável para conectar com o mongoDB, que tinha antes:
		nano .env

		- Para iniciar o projeto:
			pm2 start name
			(EX: pm2 start Agenda)

		- Para pausar o projeto:
			pm2 stop name

		OBS: Se eu reinciar o servidor ele não vai iniciar automaticamente o projeto, para resolver isso eu executo o comando:
			pm2 startup
			
			- Nesse caso, ele irá pedir para você executar outro comando, ai você copia ela e executa esse comando
	
	6.5 - Liberando acesso somente com o ip do nosso servidor:
		no MongoDB Atlas você vai na parte de "Network Acess" e vai configurar um ip de entrada com o ip ou domínio do seu servidor

	Resumo: Todas vez que eu for acessar minha aplicação pelor servidor eu vou utilizar os comandos (Dentro da pasta do projeto):
		- Iniciando servidor:
			npm start
		- Iniciando o pm2:
			pm2 start nome-ou-id-da-aplicacao
			(EX: pm2 start Agenda)
		- Verificando se está funcionando:
			curl http://localhost:numero-da-porta
			(EX: curl http://localhost:3000)


## 7 - Instalando e configurando o NGINX para fazer proxy reverso (as requisições vão chegar no nosso endereço seja o ip ou domínio, ai o NGINX irá receber na porta 80 ou na 443 (http ou https, depende se você tem ou não um domínio), quando essa requisição chegar o NGINX vai passar essa requisição para dentro do nosso servidor na porta 3000 no caso do exemplo para falar com o node, ai o node respode e NGINX volta para o cliente)

	7.1 - Acesse o servidor se você já não tiver acessado

	7.2 - Instalando o nginx:
		sudo apt install nginx

		- Para verificar se ele já foi iniciado:
			sudo systemctl status nginx

	7.3 - Com isso você já deve acessar o nginx pelo seu ip do seu servidor

	7.4 - Agora você tem que configurar o proxy reverso (mude para o seu ip ou domínio os campos "server_name", "#access_log" e "error_log", também na parte do "proxy_pass", trocar o número da porta que você está usando):

		server {
			listen 80;
			listen [::]:80;

			# Add index.php to the list if you are using PHP
			index index.html index.htm index.nginx-debian.html index.php;

			server_name ip-ou-domínio;

			location = /favicon.ico { access_log off; log_not_found off; }
		
			location / {
				proxy_pass http://localhost:número-da-porta;
				proxy_http_version 1.1;
				proxy_set_header Upgrade $http_upgrade;
				proxy_set_header Connection 'upgrade';
				proxy_set_header Host $host;
				proxy_cache_bypass $http_upgrade;
			}

			# deny access to .htaccess files, if Apache's document root
			# concurs with nginx's one
			#
			location ~ /\.ht {
				deny all;
			}

			location ~ /\. {
				access_log off;
				log_not_found off;
				deny all;
			}

			gzip on;
			gzip_disable "msie6";

			gzip_comp_level 6;
			gzip_min_length 1100;
			gzip_buffers 4 32k;
			gzip_proxied any;
			gzip_types
				text/plain
				text/css
				text/js
				text/xml
				text/javascript
				application/javascript
				application/x-javascript
				application/json
				application/xml
				application/rss+xml
				image/svg+xml;

			access_log off;
			#access_log  /var/log/nginx/ip-ou-domínio-access.log;
			error_log   /var/log/nginx/ip-ou-domínio-error.log;

			#include /etc/nginx/common/protect.conf;
		}
	
	7.5 - Agora vamos no servidor para colocar a configuração de cima no nginx:
		sudo nano /etc/nginx/sites-enabled/qualquer-nome(geralmente nome do domínio se tiver)

		7.5.1 - Salve e saia da pasta

		7.5.2 - Verifique se a pasta foi criada:
			cd /etc/nginx/sites-enabled/
			ls
		
		7.5.3 - Para tirar o default é so dar um :
			sudo rm default

		7.5.1 - Confirmar se tudo está funcionando:
			sudo nginx -t

		7.5.5 - Reiniciando a aplicação:
			sudo systemctl restart nginx

		7.5.6 - Já pode acessar pelo domínio ou pelo ip a aplicação

		OBS: Lembrando que esse método é o http, e não https. Para poder fazer com https tem que ter domínio


## 8 - Fazendo alterações na aplicação:
	8.1 - Adcionar todos os arquivos modificados:
		git add .

	8.2 - Commitar os arquivos:
		git commit -am 'descrição'

	8.3 - Enviar para o servidor:
		git push agenda master

	8.4 - Como ele foi para a pasta cópia do projeto, eu tenho que passar as alaterações da copia para o principal:
		ssh ip-ou-dominio "git -C /home/nome-usuario/nome-da-pasta-principal/ pull pasta-principal master"
		(EX: ssh 33.195.57.28 "git -C /home/Bruno/agenda/ pull agenda master")
		
