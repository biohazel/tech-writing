# Meu Primeiro Full Node — Bitcoin Base

> Esta é uma série composta de três artigos. Bitcoin Base, Lightning Network e BTCPay Server.

Se você chegou até aqui, provavelmente já se perguntou: 

#### Como me tornar um indivíduo com alto grau de soberania financeira neste novo paradigma tecnológico e econômico que estamos vivendo e ajudando a construir?

Uma das etapas avançadas de entendimento e uso deste sistema é a criação e gerenciamento de um full node. Lembrando que existem [inúmeras formas](https://bitcoiner.guide/node/diy/) de se rodar um nó Bitcoin, inclusive montando manualmente baixando os repositórios oficiais dos protocolos no GitHub. Este tutorial pode ser útil para Jr. developers e pessoas tecnicamente curiosas, mas que ainda são iniciantes no Bitcoin e querem explorar os benefícios de rodar nodes completos em casa pela primeira vez. Esta solução é especialmente atraente se você gosta de fuçar no ecossistema web5, Nostr, e outras aplicações descentralizadas. 

Neste primeiro artigo vou compartilhar com vocês uma experiência da vida real sobre como criar e manter um nó completo de Bitcoin. No segundo artigo, vamos aprender a fazer transações Lightning conversando diretamente com seu node e seus canais, e no terceiro artigo vamos chegar ao ponto de usá-lo para receber pagamentos por algum site na internet com BTCPay Server. 

Tive algumas experiências montando e rodando nodes, com diferentes níveis de sucesso. Se tem um conselho que eu queria que tivessem me dado mais firmemente no passado, é fique longe das Raspberry Pis. É um hardware subpar. Demora quase 5 dias o Initial Block Download do Bitcoin, ou seja, aquele primeiro download e indexação de todo histórido da blockchain do Bitcoin desde 2009. Se você já tem a vida ocupada, não gaste tempo e dinhieor com isso. Não tem como ficarmos esperando muito dela. 

Hoje, se eu estivesse sem tempo mas quisesse levantar um node mesmo assim, eu compraria direto no site da Umbrel. No momento ela parece estar valendo a pena, só não sei a situação dos impostos sobre esse valor. 

![node-umbrel-plug-play](https://github.com/biohazel/tech-writing-illustrations/blob/master/node-umbrel-plug-play.jpg?raw=true)

Vejam que que tenho uma Pi 4, olha a diferença de performance. Sente essa dor. 

![cpu-performance](https://github.com/biohazel/tech-writing-illustrations/blob/master/cpu-performance.png?raw=true)

Na época, o custo era R$3500 e eu fiquei com receio da receita federal taxar meu produto. Então decidi que seria mais seguro comprar as peças e montar uma máquina equivalente. 

No meu caso, quando comprei o hardware eu olhei no site da Umbrel qual processador estava vindo nos mini servers deles e comprei igual. O meu é um Beelink, esse aqui. 

![mini-pc-beelink](https://github.com/biohazel/tech-writing-illustrations/blob/master/mini-pc-beelink.png?raw=true)

Também comprei um SSD de 2T, abri e instalei nele. Precisa daquelas chaves-de-fenda de precisão para eletrônicos. A blockchain do Bitcoin é uma das coisas que mais ocupa espaço. Agora é Fevereiro de 2024 e ela está em aproximadamente ~549 GB.

![beelink-ssd](https://github.com/biohazel/tech-writing-illustrations/blob/master/beelink-ssd.jpg?raw=true)

Para começar com a parte do software, precisamos preparar um flash drive com o sistema operacional que vamos usar para instalar o Umbrel, que será o Ubuntu Server. Vamos até o site da Ubuntu e baixar uma imagem iso para gravar no flash drive. 

Uma imagem iso é um arquivo que contém uma cópia exata de um sistema de arquivos. No contexto do Ubuntu Server, a iso contém tudo necessário para instalar e executar o sistema operacional, preservando a estrutura e os arquivos exatamente como devem ser no disco ou flash drive.

Gravar uma imagem iso em um flash drive vai além de simplesmente copiar arquivos. É necessário escrever a imagem de forma que o flash drive seja inicializável. Isso significa configurar o flash drive de modo que o computador possa iniciar ou bootar a partir dele e acessar o instalador do sistema operacional. 

Vamos baixar o Ubuntu Server. Acesse o site oficial e baixe a versão LST (Long Term Support).

![ubuntu-server-software](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-software.jpg?raw=true)

Em seguida, hora de preparar o software que vamos usar para gravar a imagem no flash drive. Programas como Balena Etcher para MacOS automatizam esse processo, garantindo que o drive seja corretamente formatado e preparado para iniciar o sistema no seu mini PC. Se você usa Windows ou Linux, peça para ChatGPT te dar alternativas ao Balena Etcher. 

Baixe e instale o Balena Etcher. 

![balena-etcher-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-1.jpg?raw=true)

Agora insira o flash drive e com o Balena Etcher, copie a imagem iso para o flash drive. 

![balena-etcher-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-2.jpg?raw=true)
![balena-etcher-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-3.jpg?raw=true)
![balena-etcher-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-4.jpg?raw=true)

Dispositivo pronto, é hora de ligar um teclado e um monitor ao mini PC para acessarmos a BIOS e configurar um boot por esse dispositivo. 

Para acessar a BIOS (ou UEFI, que é a versão mais moderna da BIOS) de um mini PC, normalmente você precisa estar fisicamente presente e usar um teclado conectado diretamente ao dispositivo. Isso ocorre porque a BIOS é acessada durante o processo de inicialização do hardware, antes que o sistema operacional ou qualquer software de rede, como um servidor SSH (Secure Shell), seja carregado. Portanto, a BIOS opera fora do ambiente onde comandos remotos ou conexões de rede estariam disponíveis.

Vamos fazer isso acontecer. Esse é meu setup. Como tenho essa TV sobrando no quarto onde fica o modem de internet, eu liguei nela mesmo. Lembrando que vamos precisar conectar um cabo de rede ao seu node. 

![setup-hardware](https://github.com/biohazel/tech-writing-illustrations/blob/master/setup-hardware.jpg?raw=true)

Veja que tenho um nobreak também. Algo importante quando se gerencia um nó Lightning é garantir uma saúde de uptime, um mínimo tempo possível offline durante a vida dos canais. Se você mora em um local com muitos picos de energia, é legal ter um nobreak e ligar sua internet e seu node nele. 

Insira o flash drive com Ubuntu Server no mini PC e ligue-o. Aperte Del—dependendo da máquina pode ser outra tecla—para entrarmos na BIOS. A minha já estava configurada para ler a unidade de flash drive primeiro. 

![ubuntu-server-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-1.jpg?raw=true)

Agora save and exit, que ele já inicia lendo o drive USB que coloquei. 

![ubuntu-server-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-2.jpg?raw=true) 

Agora começando… 

![ubuntu-server-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-3.jpg?raw=true) 

Veja essa sequência de telas, é bem intuitivo. Primeiro ele vai pedir para vc apertar umas teclas para identificar a configuração do seu teclado. 

![ubuntu-server-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-4.jpg?raw=true) 

![ubuntu-server-5](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-5.jpg?raw=true) 

Agora vamos instalar o Ubuntu Server default mesmo. 

![ubuntu-server-6](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-6.jpg?raw=true) 

![ubuntu-server-7](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-7.jpg?raw=true) 

![ubuntu-server-8](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-8.jpg?raw=true) 

![ubuntu-server-9](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-9.jpg?raw=true) 

![ubuntu-server-10](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-10.jpg?raw=true) 

![ubuntu-server-11](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-11.jpg?raw=true) 

Aqui nós vamos escolher Continue. 

![ubuntu-server-12](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-12.jpg?raw=true) 

Hora de setar as credenciais do seu nó. Eu faço o mais simples possível para lembrar depois, mas lembre de anotar isso aqui muito bem. 

![ubuntu-server-13](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-13.jpg?raw=true) 

![ubuntu-server-14](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-14.jpg?raw=true) 

Vamos dar um Skip no Ubuntu Pro. 

![ubuntu-server-15](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-15.jpg?raw=true) 

Aqui selecione o install OpenSSH server, um software que implementa o protocolo SSH para você acessar seu node remotamente de outras máquinas.

![ubuntu-server-16](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-16.jpg?raw=true) 

Vai seguindo a sequência até o momento de reiniciar. Aqui no caso não vamos instalar nada. Depois o Umbrel cuida disso. 

![ubuntu-server-17](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-17.jpg?raw=true) 

![ubuntu-server-18](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-18.jpg?raw=true) 

![ubuntu-server-19](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-19.jpg?raw=true) 

Aqui você pode retirar seu flash drive. Eu retirei e mesmo assim ele me levou para uma tela que avisa que você precisa tirar ele. De qualquer jeito vai dar certo. Retire e aperte Enter. Depois mais um enter para pedir suas credenciais de login. 

![ubuntu-server-20](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-20.jpg?raw=true) 

![ubuntu-server-21](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-21.jpg?raw=true) 

![ubuntu-server-22](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-22.jpg?raw=true) 

Prontinho! Ubuntu Server instalado e pronto para ser gerenciado remotamente. 

Vamos fazer o gerenciamento e a instalação do Umbrel à distância. Sei que fiz a parte do Balena Etcher no Mac mas agora vou operar o restante das configurações do node de uma máquina Linux. 

Assumimos que você já esteja com o cabo de rede ligado. Vamos descobrir qual o endereço de IP da sua máquina. É o momento de olhar embaixo do seu roteador o endereço de IP dele próprio e suas credenciais de acesso. Dentro do sistema do roteador vamos descobrir o IP do seu nó. Basta digitar o IP do seu roteador em algum navegador de internet e seguir as instruções de login. Olha como é fácil identificar, pois o nome que demos ao nó nas primeiras credenciais de acesso é o que aparece listado dentre os IPs em atividade nessa rede. 

![ip-node](https://github.com/biohazel/tech-writing-illustrations/blob/master/ip-node.png?raw=true) 

Agora, vamos acessar esse node remotamente. Abra seu Terminal (em sistemas Linux ou macOS) ou PowerShell (em sistemas Windows). Tente acessar seu nó usando o comando:

```ssh usuario@endereco_ip```

Certifique-se de substituir usuario pelo seu nome de usuário real e endereco_ip pelo endereço IP do nó que você deseja acessar. O meu no caso é 

```ssh scalar@192.168.15.55```

![ubuntu-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-1.png?raw=true) 

Como eu já tive um setup de servidor nesse IP no passado, ele está identificando um conflito de chaves SSH. Mais especificamente, o nome do servidor mudou. 

![ubuntu-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-2.png?raw=true) 

Então precisamos remover a chave de acesso antiga e criar uma nova. Com a chave removida, só tentar fazer o login novamente. 

```ssh-keygen -f "/home/ubuntu/.ssh/known_hosts" -R "192.168.15.55````

![ubuntu-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-3.png?raw=true) 

Agora é o momento de preparar o SSD. formatar, e montar. Depois adicionar o mount point a /etc/fstab para que ele persista por vários reboots. 

Comece com o comando ```lsblk``` 

![ubuntu-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-4.png?raw=true) 

No meu caso consigo ver que está em sdb1. Agora vamos preparar este disco, formatando a partição sdb1 com ext4. 

```sudo mkfs.ext4 /dev/sdb1```

![ubuntu-5](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-5.png?raw=true) 

Pronto, agora vamos criar o diretório que será o mount point. 

```sudo mkdir -p /mnt/umbrel```

Em seguida realizar o mount. 

```sudo mount /dev/sdb1 /mnt/umbrel```

![ubuntu-6](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-6.png?raw=true) 

Anote a UUID da sua partição. Para garantir que o SSD seja montado automaticamente na inicialização, você pode adicionar uma entrada em /etc/fstab. 

Use a UUID (própria do seu sistema) para adicionar uma linha semelhante a esta no arquivo /etc/fstab. Primeiro você precisa abrir /etc/fstab com um editor de textos. No caso usarei o Vim. 

```sudo vim /etc/fstab```

Agora adicione essa linha abaixo e a salve. 

```UUID=seu-uuid-aqui /mnt/umbrel ext4 defaults 0 2```

```UUID=c4852d3e-6435-45c5-805f-420a959a1568 /mnt/umbrel ext4 defaults 0 2```

![ubuntu-7](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-7.png?raw=true) 

Agora vamos usar o comando ```sudo mount -a``` para montar todas as partições listadas no /etc/fstab que ainda não estão montadas.

Para garantir que não houve erros na sua entrada do /etc/fstab, vamos verificar o lsblk.

![lsbk-final](https://github.com/biohazel/tech-writing-illustrations/blob/master/lsblk-final.png?raw=true) 

Agora vamos nos localizar bem onde o disco está montado para instalar o Umbrel. 

```cd /mnt/umbrel```

Dê uma olhada no site da Umbrel quais as orientações atuais para instalação em Linux. Tem versão nova do OS deles para sair em Março!

![umbrel-0](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-0.png?raw=true) 

Hora de usar o comando de instalação em Linux. 

```curl -L https://umbrel.sh | bash```

![ubuntu-8](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-8.png?raw=true) 

E voilá!

![ubuntu-9](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-9.png?raw=true) 

Agora vem o momento mágico. Coloca a musiquinha Umbrella da Ember Island para tocar e digite o nome do seu servidor.local no seu navegador. No caso, o meu é: 

scalar.local

![umbrel-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-1.png?raw=true) 

Agora preencha com um nome e senha.

![umbrel-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-2.png?raw=true) 

![umbrel-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-3.png?raw=true) 

Agora vamos escolher nosso primeiro aplicativo, o Bitcoin Core. Precisamos dele como pré-requisito para instalar o protocolo Lightning por cima e o próprio BTCPay Server depois. 

![umbrel-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-4.png?raw=true) 

![umbrel-5](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-5.png?raw=true) 

![umbrel-6](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-6.png?raw=true) 

Agora vamos esperar o IBD, download inicial da blockchain do Bitcoin. Com esse processador, deve demorar umas 15 horas. Mas vamos contar. Essa foto de quando comecei a baixar o Bitcoin Core 26.0.0 foi tirada em 14 de Fevereiro de 2024 às 7:06PM. Assim que terminar de baixar, daremos continuidade à parte 2 deste artigo: instalando um node Lightning, abrindo canais, e gerenciando eles remotamente via Zeus. Uma palavra de adoração à Zeus que me ajudou a fechar meus canais e recuperar meus fundos quando eu quebrei meu node antigo fuçando demais. 






































