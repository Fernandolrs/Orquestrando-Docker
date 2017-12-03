 <h1>Tutorial Orquestrando Docker</h1>

<h3>O que é o Docker</h3>

<p>Docker é uma plataforma Open Source escrita em linguagem de programação <a href="https://golang.org/doc/">GO</a>, que facilita a criação e administração de recursos, bibliotecas de kernel em  ambientes isolados.</p>
<p>O Docker possibilita o empacotamento de uma aplicação ou ambiente inteiro dentro de um ou vários containers, e a partir desse momento o ambiente inteiro torna-se portável para qualquer outro Host que contenha o Docker instalado. Não há necessidade de ajustes de ambiente para o correto funcionamento do serviço, o ambiente é sempre o mesmo, configure-o uma vez e replique-o quantas vezes quiser.</p>

 <h3>Orquestrando docker</h3>

<p>Quando as aplicações são escaladas por muitos sistemas host, a capacidade de gerenciar cada sistema host se torna  complexa. A orquestração é um termo abrangente que refere-se ao agendamento de contêiner, gerenciamento de cluster, e o provisionamento de hosts adicionais.</p>
<p>Neste ambiente, <i>"agendamento"</i> refere-se à capacidade de um administrador carregar um arquivo de serviço em um sistema host, o qual opcionalmente estabelece restrições de agendamento de acordo com suas necessidades para executar um contêiner específico. A maior responsabilidades dos agendadores é a seleção de host, o agendador geralmente fica encarregado de selecionar automaticamente um host, e responsável pela execução com base nesses requisitos.</p>
<p>Os agendadores geralmente definem uma política padrão de agendamento. Isto determina como os serviços são agendados quando nenhuma entrada é fornecida pelo administrador. Por exemplo, um agendador poderia escolher colocar novos serviços em hosts com o menor número de serviços atualmente ativos.</p>

<h2>Tutorial Orquestrando Dockers em aplicação Producer/Consumer.</h2>

Este tutorial foi desenvolvido para demostrar a orquestração de uma aplicação utilizando dockers sendo orquestrada pelo <a href="https://www.rabbitmq.com/">RabbitMQ</a>.
O foco deste tutorial é a Orquestração de Dockers, por este motivo utilizaremos um projeto existente desenvolvido por  Gabriel Feitosa que esta disponivel no <a href="https://github.com/gabrielfeitosa/scaling-java-with-docker">github</a>.


<h3>Instalando e Configurando o Docker no UBUNTU 16.04</h3>

Os passos a seguir devem ser realizados para instalação dos pacotes do Docker, realizando dowlowd do repositório oficial. A seguir demonstraremos como fazer isso passo a passo.

<h3>Passo 1 - Instale o Docker</h3>

Primeiro, vamos instalar o Docker. Adicione ao sistema a chave GPG oficial do repositório do Docker:
<pre><code>
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
</pre></code>

Adicione o repositório do Docker às fontes do APT:
<pre><code>
sudo apt-add-repository 'deb https://apt.dockerproject.org/repo ubuntu-xenial main'
</pre></code>

Atualize o banco de dados de pacotes com os pacotes do Docker a partir do novo repositório adicionado:
<pre><code>
sudo apt-get update
</pre></code>

Finalmente, instale o Docker:
<pre><code>
sudo apt-get install -y docker-engine
</pre></code>

O Docker agora será instalado, o daemon iniciado, e o processo habilitado para iniciar no boot. Verifique que ele está executando:
<pre><code>
sudo systemctl status docker
</pre></code>

Caso o Daemon não esteja ativo, inicie o serviço:
<pre><code>
sudo systemctl start docker
</pre></code>

Enfim, reinicie o sistema para testar inicialização do serviço do docker.

<pre><code>
sudo reboot
</pre></code>

<h3>Passo 2 - Clone o projeto do repositorio do Git</h3>

Clone o repositorio localizado no Github denominado <a href="https://github.com/Fernandolrs/Orquestrando-Docker">Orquestrando-Docker</a>.

Vale resaltar que este projeto não foi desenvolvido por nós, estamos apenas demostrando gerenciamento de Docker. Entretanto adicionamos arquivos para que o <b>Docker-compose</b> não necessita-se de configuração extra.

<h3>Passo 3 - Instale Docker-compose</h3> 

O projeto Producer/Consumer já possui as dependencias necessarias para o <strong>docker-compose</strong> ser executado. Entretanto alguns passos ainda são necessarios. O Docker-compose também é um orquestrador, utilizaremos ele para orquestrar ha criação dos conteiners, neste tutorial destacaremos mais informações sobre ele.

Instale o Docker-Compose

<pre><code>
sudo apt-get install docker-compose
</code></pre>

Verifique a Versão do Docker compose:

<pre><code>
sudo docker-compose -v
</code></pre>

Após a verificação, seguiremos para os proximo passo, e eventualmente destacaremos os comandos necessarios para a execução deste projeto.

<h3> Passo 4 - Executando Containers Producer/Consumer </h3>

O <b>Docker-compose</b> por ser um orquestrador, ele cria os Dockers atraves do arquivo <strong>docker_conpose.yml</strong>, para este tutorial utilizamos um projeto existente , pois nosso foco e mostrar o gerenciamento de conteiners.

Verifique atraves do Docker que nenhum conteiner foi criado ate o momento:

<pre><code>
sudo docker images
</code></pre>

Situando que estamos dentro da pasta do projeto execute o comando para criarmos os conteiners, cada conteiner é criando atraves do <strong>Dockerfile</strong>, que esta presente neste diretorio e no SMSConsumidor e no SMSProdutor.
O comando <strong>build</strong> é responsavel por ler o Dockerfile e criar os conteiner conforme apersonalização de cada conteiner.

<pre><code>
sudo docker-compose build
</code></pre>

Se tudo ocorreu bem ao verificarmos o docker veremos as imagens do projeto criadas.

<pre><code>
sudo docker images
</code></pre>

Enfim vamos iniciar os conteiners, do projeto com o comando <strong>up</strong>.
<pre><code>
sudo docker-compose up
</code></pre>

<h3> Passo 5 - Gerenciando Conteiners </h3>

Para Orquestrar utilizaremos o <strong>RAbbitMQ</strong>, que dispoe de uma interface grafica para facilitar a visualização.

Agora acesse o endereço <b>localhost</b> em seu navegador, na porta especificada, caso o <b>RabbitMQ</b> solicite <i>login</i> e senha. Informe <b>guest</b> para ambos os campos.

<pre>
localhost:15672/#/
</pre>

No momento vera o grafico do produtor em sentido exponecial. Para que haja melhor performace, subiremos mais conteiner consumidores passando o parametro <b>scale</b>. 
<pre></code>
sudo docker-compose scale smsconsumer=4
</code></pre>

Lembrando que o docker-compose criara mais conteiners baseando-se no numero ja existente deles menos quantidade passada como parametro, no sentido de diminuir alguns containers, para ver o que acontece, reduziremos 2 deles. Substitua o numero final para 2 containers, que o Docker-compose automaticamente suspenderá a execução dos containers. Experimente adicionar mais Producer e ver a variação dos graficos.

<pre></code>
sudo docker-compose scale smsconsumer=2
</code></pre>

Para ver o status dos containers, invoque outro prompt de comando execute o comando <b>stats</b>.
<pre></code>
sudo docker stats
</code></pre>

Se desejar pode remover ou parar todos os conteiner.
<pre><code>
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q) 
</code></pre>

Ou pode remover apenas 1:
<pre><code>
sudo docker rm Nome_container 
</code></pre>

Para parar o serviço do Docker-compose execute o comando <b>down</b>.
<pre></code>
sudo docker-compose down
</code></pre>


<h3> Conclusão</h3> 

<p>Este tutorial sobre Orquestração de Dockers, foi proposto na disciplina de Sistemas Operacionais, com objetivo de demonstrar uma aplicação sobre o tema gerenciamento de processadores e distribuição de processos. Entre as necessidade de dependências na configuração do projeto, o docker é muito produtivo, possuindo um alto potencial para suprir o mercado, sendo de grande valia estudar mais a fundo suas aplicações e seu funcionamento para aplicações futuras.</p>


<h3>Saiba Mais</h3>
<dl>
<dd>O Ecossistema do Docker: <a href="https://www.digitalocean.com/community/tutorials/o-ecossistema-do-docker-agendamento-e-orquestracao-pt"> Agendamento e Orquestração </a></dd>
 <dd><a href="http://flaviosilveira.com/2016/microservicos/">Microserviços</a></dd>
<dd>Link entre Containers - <a href="https://www.mundodocker.com.br/link-entre-containers/">Mundo Docker</a></dd>
<dd>Criando uma imagem: <a href="https://woliveiras.com.br/posts/Criando-uma-imagem-Docker-personalizada/">Docker personalizada</a></dd>
<dd>Criando suas prórias <a href="http://techfree.com.br/2016/03/criando-sua-propria-imagem-docker/">imagens Docker</a></dd>
</dl>
