
 <h1>Tutorial Orquestrando Docker</h1>

<h3>O que é o Docker</h3>

<p>Docker é uma plataforma Open Source escrita em linguagem de programação <a href="https://golang.org/doc/">GO</a>, que facilita a criação e administração de recursos, bibliotecas de kernel em  ambientes isolados.</p>
<p>O Docker possibilita o empacotamento de uma aplicação ou ambiente inteiro dentro de um ou vários containers, e a partir desse momento o ambiente inteiro torna-se portável para qualquer outro Host que contenha o Docker instalado. Não há necessidade de ajustes de ambiente para o correto funcionamento do serviço, o ambiente é sempre o mesmo, configure-o uma vez e replique-o quantas vezes quiser.</p>

 <h3>Orquestrando docker</h3>

<p>Quando as aplicações são escaladas por muitos sistemas host, a capacidade de gerenciar cada sis

tema, se torna  complexa. Para isso surgiu o termo orquestração que refere-se ao agendamento de contêiner, gerenciamento de cluster, e o provisionamento de hosts adicionais.</p>
<p>Neste ambiente, <i>"agendamento"</i> refere-se à capacidade de um administrador carregar um arquivo de serviço em um sistema host, o qual opcionalmente estabelece restrições de agendamento de acordo com suas necessidades para executar um contêiner específico. A maior responsabilidades dos agendadores é a seleção de host, o agendador geralmente fica encarregado de selecionar automaticamente um host, é responsável pela execução com base nesses requisitos.</p>
<p>Os agendadores geralmente definem uma política padrão de agendamento. Isto determina como os serviços são agendados quando nenhuma entrada é fornecida pelo administrador. Por exemplo, um agendador poderia escolher colocar novos serviços em hosts com o menor número de serviços atualmente ativos.</p>

<h2>Orquestrando Docker em aplicação Producer/Consumer.</h2>

Este tutorial foi desenvolvido para demonstrar a orquestração de uma aplicação utilizando docker sendo orquestrada pelo <a href="https://www.rabbitmq.com/">RabbitMQ</a>.
O foco deste tutorial é a Orquestração do Docker, por este motivo utilizaremos um projeto existente desenvolvido por  Gabriel Feitosa que esta disponivel no <a href="https://github.com/gabrielfeitosa/scaling-java-with-docker">github</a>.


<h3>Instalando e Configurando o Docker no UBUNTU 16.04</h3>

Os passos a seguir devem ser realizados para instalação dos pacotes do Docker, realizando download do repositório oficial. A seguir demonstraremos como fazer isso passo a passo.

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

<h3>Passo 2 - Clone o projeto do repositório do Git</h3>

Clone o repositorio localizado no Github denominado <a href="https://github.com/Fernandolrs/Orquestrando-Docker">Orquestrando-Docker</a>.

Vale ressaltar que este projeto não foi desenvolvido por nós, estamos apenas demonstrando gerenciamento de Docker. Entretanto adicionamos arquivos para que o <b>Docker-compose</b> não necessita-se de configuração extra.

<h3>Passo 3 - Instale Docker-compose</h3>

O projeto Producer/Consumer já possui as dependências necessárias para o <strong>docker-compose</strong> ser executado. Entretanto alguns passos ainda são necessários. O Docker-compose também é um orquestrador, utilizaremos ele para orquestrar a criação dos contêineres, neste tutorial destacamos mais informações sobre ele.

Instale o Docker-Compose

<pre><code>
sudo apt-get install docker-compose
</code></pre>

Verifique a Versão do Docker compose:

<pre><code>
sudo docker-compose -v
</code></pre>

Após a verificação, seguiremos para os próximo passo, e eventualmente destacamos os comandos necessários para a execução deste projeto.

<h3> Passo 4 - Executando Containers Producer/Consumer </h3>

O <b>Docker-compose</b> por ser um orquestrador, ele cria os Dockers através do arquivo <strong>docker_conpose.yml</strong>.

Verifique através do Docker que nenhum container foi criado até o momento:

<pre><code>
sudo docker images
</code></pre>

Situando que estamos dentro da pasta do projeto execute o comando para criarmos os contêineres, cada contêiner é criado através do <strong>Dockerfile</strong>, que está presente neste diretório e no SMSConsumidor e no SMSProdutor.
O comando <strong>build</strong> é responsável por ler o Dockerfile e criar os contêiner conforme a personalização de cada contêiner.

<pre><code>
sudo docker-compose build
</code></pre>

Se tudo ocorreu bem ao verificarmos o docker veremos as imagens do projeto criadas.

<pre><code>
sudo docker images
</code></pre>

Enfim vamos iniciar os contêineres, do projeto com o comando <strong>up</strong>.
<pre><code>
sudo docker-compose up
</code></pre>

<h3> Passo 5 - Gerenciando Containers </h3>

Para Orquestrar utilizaremos o <strong>RAbbitMQ</strong>, que dispõe de uma interface gráfica para facilitar a visualização.

Agora acesse o endereço <b>localhost</b> em seu navegador, na porta especificada, caso o <b>RabbitMQ</b> solicite <i>login</i> e senha. Informe <b>guest</b> para ambos os campos.

<pre>
localhost:15672/#/
</pre>

No momento verá o gráfico do produtor em sentido exponencial. Para que haja melhor performance, subiremos mais conteiner consumidores passando o parâmetro <b>scale</b>.
<pre></code>
sudo docker-compose scale smsconsumer=4
</code></pre>

Lembrando que o docker-compose criará mais contêineres baseando-se no número já existente deles menos quantidade passada como parâmetro, no sentido de diminuir alguns containers, para ver o que acontece, reduzimos 2 deles. Substitua o número final para 2 containers, que o Docker-compose automaticamente suspenderá a execução dos containers. Experimente adicionar mais Producer e ver a variação dos gráficos.

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

Ou pode remover apenas 1, substituindo o <b>Nome_container</b> pelo do container:
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
 <dd>Criando cluster Docker com <a href="https://www.mundodocker.com.br/docker-1-12/">Docker Swarm</a>
</dl>
