# PhD_CIn_UFPE_BlockCompass

# 1. Instando o Hyperledger Fabric

https://medium.com/trubr/instala%C3%A7%C3%A3o-do-hyperledger-fabric-no-ubuntu-parte-1-aa821c2bec20

sudo apt-get update sudo apt-get upgrade sudo apt-get dist-upgrade sudo apt-get install curl sudo apt-get install libtool sudo apt-get install libltdl-dev sudo apt-get install wget sudo apt-get install python-pip sudo apt-get install make Instalação do GO

O GO precisa ser instalado para que possamos compilar o hyperledger fabric. Importante ter todas as variáveis de ambiente configuradas corretamente pois elas são utilizadas, exatamente com estes nomes, pelos scripts de configuração do Fabric. Reforçando, não use ‘root’.

cd $HOME mkdir $HOME/gopath wget https://redirector.gvt1.com/edgedl/go/go1.9.2.linux-amd64.tar.gz tar -xvzf go1.9.2.linux-amd64.tar.gz rm go1.9.2.linux-amd64.tar.gz export GOPATH=$HOME/gopath export GOROOT=$HOME/go export PATH=$PATH:$GOROOT/bin mkdir -p $GOPATH/src/github.com/hyperledger/ mkdir -p $GOPATH/src/github.com/trubr/

Vamos editar o arquivo do perfil do seu usuário para que as variáveis sejam criadas sempre que você logar, senão você precisará fazer o export das variáveis manualmente a cada login.

vi ~/.bashrc #Inserir ao final #GO VARIABLES export GOROOT=$HOME/go export GOPATH=$HOME/gopath export PATH=$PATH:$GOROOT/bin #END GO VARIABLES source ~/.bashrc

Ao final desse passo você já deve conseguir executar o comando “go version” para conferir a versão do GO no servidor. Se não funcionar, revise se as variáveis estão corretamente definidas. A saída deverá ser algo como:

‘go version go1.9.2 linux/amd64’ Instalação do Docker e do Node.JS

Vamos instalar o docker e o node.js agora.

curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash - sudo apt-get install -y nodejs sudo apt-key adv — keyserver hkp://p80.pool.sks-keyservers.net:80 — recv-keys 58118E89F3A912897C070ADBF76221572C52609D sudo apt-add-repository ‘deb https://apt.dockerproject.org/repo ubuntu-xenial main’ sudo apt-get update apt-cache policy docker-engine sudo apt-get install -y docker-engine sudo systemctl status docker sudo service docker start sudo systemctl enable docker

Existem várias formas de instalar o docker dependendo da sua distribuição. Você pode por exemplo simplesmente baixar o arquivo “deb” e executar ‘sudo dpkg -i docker-ce_xx.xx~ubuntu_amd64.deb’, basta procurar o arquivo deb correspondente no repositório.

Ao final dessa etapa você já deve estar com o docker em execução. Ainda não temos nada da blockchain instalado. Vamos agora criar um usuário docker e incluir o nosso usuário atual no mesmo grupo com permissão de execução do docker, assim você não precisará usar o usuário do docker para as operações.

sudo usermod -aG docker $(whoami) sudo gpasswd -a $USER docker

Agora vamos instalar o docker-compose. Com ele podemos gerenciar a execução de várias instâncias do docker usando arquivos YAML.

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - sudo apt-key fingerprint 0EBFCD88 sudo add-apt-repository “deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable” sudo pip install docker-compose sudo chmod a+x /usr/local/bin/docker-compose

Nessa etapa você já deve conseguir executar o comando ‘docker version’ conforme a imagem abaixo. Comando ‘docker version’ esperado

Para garantir ainda que o docker está pronto você pode executar a imagem ‘hello-world’ com o comando ‘docker run hello-world’. O docker deve baixar do repositório a imagem do hello-world e exibir o retorno de execução bem sucedida igual à imagem abaixo. Comando docker run hello-world

Você deve conseguir executar também o comando ‘docker-compose -v’ para verificar a versão do docker-compose.

Se aparecer um erro de permissão considere reiniciar a sua instância do ubuntu com “sudo shutdown -r now” pois você deve conseguir executar o comando ‘docker version’ com o seu usuário de instalação, sem necessidade do ‘root’. Se após a reinicialização você ainda não conseguir executar ‘docker version’ ou o ‘docker run hello-world’ com sucesso, não siga adiante e revise a mensagem de erro e as etapas anteriores.

Dica: Se você não é familiarizado com docker, recomendo assistir esta série de 5 vídeos do canal ‘LinuxTips’, em português e muito bem explicado.

Instalação do Hyperledger Fabric

Agora efetivamente vamos instalar o Fabric. Como existem várias formas de instalação, softwares que ajudam, scripts diferentes pela rede, sugiro você salvar uma imagem do ubuntu no formato atual para que não tenha que repetir os passos anteriores caso queira testar novas formas de instalação do Fabric.

Dica: Note que não estamos mais usando ‘sudo’ nos comando abaixo. A instalação é feita com o seu usuário mesmo.

Primeiro vamos até o diretório que criamos no início e vamos baixar o fabric do github.

cd $GOPATH/src/github.com/hyperledger/ git clone https://github.com/hyperledger/fabric.git

Opcional: Se você precisar usar alguma versão específica do Fabric utilize o comando “git reset” — Por exemplo:
git reset — hard 5fb31ddd24d6441b0d499b9bb211632800044512

Com o comando acima ele irá voltar à versão para o fabric v1.0.2 caso contrário ele irá utilizar a última versão do ‘master’ do github, o que pode ser indesejado dependendo da sua necessidade.

Agora vamos compilar o fabric e garantir que ele esteja executando corretamente no servidor.

cd fabric make release-all export PATH=$PATH:$GOPATH/src/github.com/hyperledger/fabric/build/bin/ Adicione isso no ./bashrc

Dica: O comando acima faz o build de todos os componentes. Prefiro assim mas você pode compilar um a um e ir testando, como ‘make configtxgen’, ‘make cryptogen’ etc…
make configtxgen cryptogen

O comando acima pode demorar vários minutos, dependendo da configuração da sua máquina. Aproveite e tome um café ou almoce. Esperando terminar o comando make release-all

Quando terminar até o momento você ocupou 2.5Gb de espaço em disco.

Agora vamos baixar a parte pesada do fabric que são as imagens do docker de cada componente do hyperledger. Além de demorar (mais café) um pouco você precisará de pelo menos mais 3.5Gb de espaço em disco.

make docker

Ao final do comando acima você já estará com todos os componentes do fabric instalados em seu servidor no docker container. Para conferir execute o comando :

docker images

Você deve encontrar um resultado parecido com este abaixo. Se tudo funcionou corretamente então o fabric está pronto para uso.

Na parte 2 deste tutorial vamos mostrar como realizar a configuração inicial de uma business network no Fabric, desenvolvimento e deploy de chaincodes, entre outros aspectos.
About

 # 2. Instalação do MongoDB Ubuntu 22.04

https://redessy.com/como-instalar-mongodb-en-ubuntu-22-04/?expand_article=1&expand_article=1&expand_article=1


        Política de Cookies
        Política de Privacidad

Cómo Instalar MongoDB En Ubuntu 22.04

Ubuntu 22.04 es una de las últimas versiones del sistema operativo Linux más popular y ampliamente utilizado en el mundo. Si eres un entusiasta de la gestión de bases de datos y estás interesado en trabajar con MongoDB, este artículo te guiará paso a paso en el proceso de instalación de MongoDB en Ubuntu 22.04.

En este artículo, aprenderás cómo instalar MongoDB en tu sistema Ubuntu 22.04 de forma sencilla y rápida. MongoDB es una base de datos NoSQL de código abierto y orientada a documentos, que ofrece una alta escalabilidad y rendimiento. Para comenzar la instalación, te proporcionaremos los comandos necesarios para agregar el repositorio oficial de MongoDB a tu lista de fuentes de software. Luego, te guiaremos en la instalación del paquete mongodb-org para obtener la última versión estable de MongoDB. Además, te mostraremos cómo habilitar el servicio y realizar algunas configuraciones básicas. ¡Sigue leyendo para descubrir cómo aprovechar al máximo MongoDB en tu sistema Ubuntu 22.04!
Guía paso a paso para instalar MongoDB en Ubuntu 22.04: ¡Aprende a configurar y utilizar MongoDB en tu sistema operativo!

Aquí tienes una guía paso a paso para instalar MongoDB en Ubuntu 22.04:

1. Actualiza el sistema operativo:
Abre una terminal y ejecuta los siguientes comandos:
sudo apt update
sudo apt upgrade

2. Importa la clave pública de MongoDB:
Ejecuta el siguiente comando para importar la clave:
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -

3. Agrega el repositorio de MongoDB:
Ejecuta el siguiente comando para agregar el repositorio:
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -sc)/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list

4. Actualiza el sistema e instala MongoDB:
Ejecuta los siguientes comandos:
sudo apt update
sudo apt install -y mongodb-org

5. Inicia el servicio de MongoDB:
Ejecuta el siguiente comando para iniciar el servicio:
sudo systemctl start mongod

6. Configura MongoDB para que se inicie automáticamente al arrancar el sistema:
Ejecuta el siguiente comando:
sudo systemctl enable mongod

¡Listo! Ahora tienes MongoDB instalado en tu sistema Ubuntu 22.04. Puedes comenzar a configurar y utilizar esta base de datos NoSQL.

Recuerda que esta guía solo cubre la instalación básica de MongoDB. Si deseas realizar configuraciones adicionales o aprender más sobre cómo utilizar MongoDB, te recomiendo consultar la documentación oficial de MongoDB.

Espero que esta guía te sea útil. ¡Buena suerte con tu experiencia en MongoDB en Ubuntu!
¿Cómo instalar MongoDB en Linux Ubuntu?

Para instalar MongoDB en Linux Ubuntu, puedes seguir los siguientes pasos:

1. Abre la terminal en tu sistema Ubuntu.

2. Agrega la clave GPG del repositorio oficial de MongoDB ejecutando el siguiente comando:

«`bash
sudo apt-key adv –keyserver hkp://keyserver.ubuntu.com:80 –recv 4B7C549A058F8B6B
«`

3. A continuación, agrega el repositorio de MongoDB a la lista de fuentes de software de Ubuntu con el siguiente comando:

«`bash
echo «deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -sc)/mongodb-org/4.4 multiverse» | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
«`

4. Actualiza la lista de paquetes disponibles e instala MongoDB ejecutando los siguientes comandos:

«`bash
sudo apt update
sudo apt install mongodb-org
«`

5. Una vez finalizada la instalación, MongoDB se iniciará automáticamente como un servicio. Puedes verificar su estado con el comando:

«`bash
sudo systemctl status mongod
«`

6. Si deseas habilitar que MongoDB se inicie automáticamente cada vez que se inicie el sistema, ejecuta el siguiente comando:

«`bash
sudo systemctl enable mongod
«`

¡Y eso es todo! Ahora tienes MongoDB correctamente instalado en tu sistema Ubuntu. Puedes comenzar a utilizarlo para tus proyectos y aplicaciones.
¿Dónde se instala MongoDB en Linux?

En Ubuntu, MongoDB se instala en el directorio /usr/bin/mongod.
¿Qué versión de MongoDB es compatible con Windows 7?

La versión de MongoDB compatible con Windows 7 en el contexto de Ubuntu es MongoDB 4.4.

MongoDB 4.4 es la última versión estable del sistema de gestión de bases de datos NoSQL, y es compatible con Windows 7 a través de la capa de compatibilidad proporcionada por el subsistema de Windows para Linux (WSL) en Ubuntu.

Para instalar MongoDB 4.4 en Ubuntu, puedes seguir los siguientes pasos:

1. Abre una terminal en tu sistema Ubuntu.
2. Actualiza los paquetes del sistema ejecutando el siguiente comando:
«`
sudo apt update
«`
3. Instala el paquete de claves GPG de MongoDB para verificar la autenticidad del paquete que se descargará:
«`
wget -qO – https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add –
«`
4. Añade el repositorio oficial de MongoDB a la lista de fuentes de software de Ubuntu:
«`
echo «deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse» | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
«`
Nota: Reemplaza «bionic» por la versión de Ubuntu que estés utilizando.
5. Actualiza nuevamente los paquetes del sistema:
«`
sudo apt update
«`
6. Instala el paquete de MongoDB:
«`
sudo apt install mongodb-org
«`
7. Inicia el servicio de MongoDB:
«`
sudo systemctl start mongod
«`

¡Y eso es todo! Ahora tienes instalado MongoDB 4.4 en tu sistema Ubuntu y puedes comenzar a utilizarlo para tus proyectos. Recuerda consultar la documentación oficial de MongoDB para obtener más información sobre cómo configurar y administrar tu base de datos.
¿Cómo saber si tengo instalado MongoDB Ubuntu?

Para verificar si tienes MongoDB instalado en Ubuntu, puedes seguir estos pasos:

1. Abre la Terminal en Ubuntu. Puedes hacerlo presionando `Ctrl + Alt + T` o buscándola en el menú de aplicaciones.

2. Ejecuta el siguiente comando para verificar si MongoDB está instalado en tu sistema:

mongo --version

Si MongoDB está instalado, verás una línea que muestra la versión instalada. Por ejemplo:

mongoDB shell version v4.4.6

Si no tienes MongoDB instalado, verás un mensaje de error que indica que el comando `mongo` no fue encontrado.

3. Alternativamente, puedes utilizar el siguiente comando para verificar si MongoDB está corriendo como un servicio en tu sistema:

sudo service mongod status

Si MongoDB está instalado y corriendo, verás un mensaje que indica que el servicio está activo y ejecutándose.

Recuerda que MongoDB es una base de datos NoSQL y puede ser instalada manualmente en Ubuntu utilizando los comandos adecuados. Si no tienes MongoDB instalado, puedes seguir las instrucciones de la documentación oficial de MongoDB para instalarlo en tu sistema Ubuntu.
