#  Fase 04

### Módulo 3

Tarefa da  aula é escrever o texto falando sobre a diferença entre container e VM.

#### Virtual Machine (VM)

* Analogia: A VM é como um prédio inteiro do zero
* Uma máquina virtual que carrega um sistema operacional inteiro.
* É mais segura porque isola mais por ter o kernel próprio.
* Mais lenta e leva minutos para ligar.

#### Container

* Analogia: O container é como as salas comerciais dentro do prédio
* O container tem sua unidade isolada mas compartilhada, ele usa os recursos do kernel do host.
*Troca um pouco do isolamento por muita velocidade, é mais leve e sobe em segundos.

#### O que é Kernel?

É o nucleo do sistema operacional, a parte que fala diretamente com o hardware.

#### Qual e como usaria?

Usaria VMs quando precisasse de isolamento total de sistemas diferentes e containers para subir aplicações leves e rápidas no dia a dia.

---

## Módulo 4

Tarefa da aula é escrever o texto explicando sobre os 5 passos do fluxos.

### Docker run hello-world

1. O cliente Docker (CLI) envia a ordem para o Docker Daemon (o motor/serviço em segundo plano).
Quando digita o comando `docker run hello-world`, estamos falando com a interface de linha de comando (no caso o Cliente).
Ele não faz o trabalho pesado, ele só anota o pedido e repassa para o Docker Daemon, que é o motor do Docker rodando em segundo plano na Vm.

2. O Daemon verifica se a imagem "hello-world" já existe localmente.
O Daemon recebe o pedido e dá uma olhada nos arquivos da máquina para ver se já tem a imagem do hello-world salva.
Como é a primeira vez, ele não acha nada.

3. O Daemon faz um "pull" da imagem direto do Docker Hub.
Já que a imagem não está no computador, o Daemon vai até a internet e baixa a imagem oficial direto do Docker Hub, que funciona como uma grande biblioteca publica de imagens.

4. O Daemon cria uma unidade isolada: o Container
Com a imagem baixada, o Daemon usa esse arquivo como um molde para  criar o container.
Ele separa um espaço isolado no sistema  e prepara tudo para rodar, usando o próprio kernel da VM.

5. O container é executado, exibe a mensagem de sucesso e finaliza.
O container liga, roda o programa que está ali dentro (quee apenas imprime o texto "Hello from Docker")e, como a missão dele era só essa, ele desliga sozinho logo em seguida.

---

## Módulo 7 & 8

Etapa: Imagens, Tags e Camadas
Data: 25/06/26

#### 1. Evidências e Caça à informação
Comapração de tamanho das imagens (`docker images`):
- python:3.12 -> 1.62 GB (base cheia)
- python:3.12 -> 179 MB (base magra)
Diferença: a versão slim é drasticamente menor, ideal para produção.

Inspeção (`docker inspect python3.12-slim`):
- Campo `cmd` padrão encontrado: python3

Comportamento do pull sem tag (`docker pull redis`)
- Tag atribuída automaticamente: latest
Alerta: Evitar! latest é flutuante e quebra a reprodutividade.

Camadas (`docker history python:3.12`):
- Maior camada encontrada:  694MB (responsável por ex: instalações de pacotes/ferramentas)
- Quantidade de camadas com 0B: 6 camadas (metadados como cmd, env, workdir).
- hello-world é minuscula porque ela possui apenas uma camada básica que executa um binário simples compilado, sem sistema operacional.

#### 2. Conceitos chave absorvidos

Analogia:
* Imagem = A receita do bolo (imutável, empacotada, guardada na estante).
* Container = O bolo assado (instância viva, alterar o bolo não muda a receita)

DockerHub:
* Sempre preferir imagens oficiais ou de publicados verificados.
* Imagens desconhecidas = código de terceiros rodando com superpoderes no meu host.

Camadas:
* Uma imagem é uma pilha de fatias imutáveis, cada uma com um Hash único.
* Cache: Camadas idênticas são baixadas e armazenadas apenas uma vez no disco.
* Camada é pra sempre: deletar um arquivo/segredo (`.env`) em uma camada superior apenas o esconde, mas ele continua exumável nas camadas.
* O cache do build funciona de baixo pra cima. Se a camada 2 mudar, todas as de cima (3, 4...) serão recosntruidas do zero.

#### 3. Comandos essenciais para o dia a dia

* `docker images` - lista as imagens locais.
* `docker ps -a ` - lista os containers existentes.
* `docker search` - busca imagens direto no docker hub via terminal.
* `docker inspect` - raio-x completo da imagem/container em formato JSON.
* `docker histoy` - Exibe todas as camadas que compõem aquela imagem.
* `docker rmi` - Remove uma imagem (falha se tiver container atrelado a ela).

---

## Módulo 11

Etapa: Docker Profissional - Otimização e Segurança de Imagens
Data: 01/07/26

#### 1. O que foi executado:

Comparação de tamanho das imagens (docker images):
- meu-app:1.0 -> 1.02 GB (base cheia, com cache e lixo local)
- meu-app:2.0 -> 140 MB (base slim,s em cache do pip, filtrada)
Resultado: redução drástica de tamanho

Validação de privilégios (whoami):
- comando: `docker run --rm meu-app:1.0 whoami`
- retorno: root (vulnerável)

- comando: `docker run --rm meu-app:2.0 whoami`
- retorno: operario (usuario sem coroa, seguro)

#### 2. Conceitos chave absorvidos:

* `.dockerignore` impede que arquivos locais como `.git`, `__pycache__`e, principalmente, o `.env`entrem nas camadas da imagem Docker.
* Imagens base `:slim`trazem equilibrio para o mercado, evitando o peso da imagem cheia e as compatibilidades de compilação da base `alpine`.
* `--no-cache-dir`no pip evita o acumulo de arquivos temporarios de download dentro da camada do container.
* O principio do menor privilégio: isntalar os pacotes como `root`e rebaixar o usuario para `operario`no final do Dockerfile protege o host caso o container seja invadido.

#### 3. A Diferenca entre `.gitignore` e `.dockerignore`:
* `.gitignore`: diz ao git o que não salvar no historico e não subir para o GitHub (protege o código na nuvem).
* `.dockerignore`: diz ao Docker o que não incluir no build do container. (protege o ambiente de produção contra vazamento de segredos `.env` e imagens infladas).

---

## Módulo 12

* Containers são descartáveis: Se o container morre, os dados gerados dentro dele somem.
* Volumes são persistentes: Isolam os dados cruciais (como banco de dados e uploads) do ciclo de vida do container. O container vira apenas o motor, enquanto os dados ficam guardados no cofre gerenciado pelo Docker

#### Desafio da etapa:

Ao rodar o comando do novo container **banco2**, os dados inseridos no primeiro container continuam intactos.

-> Comando: `docker exec -it banco2 psql -U postgres -c "SELECT * FROM recrutas;"`
-> Resultado: nome
            ------
            voce (1 row)

Este comando mostra onde o Docker gerencia os arquivos fisicamente no host (no territorio do Docker) `docker volume inspect cofre-pg`

-> Mountpoint : `/var/lib/docker/volumes/cofre-pg/_data"`


* Volume: Usado para dados de produção (como banco de dados), pois o Docker gerencia o local de forma segura e isolada.
* Bind Mount: Usado em desenvolvimento, pois espelha uma pasta local da máquina para editar o código em tempo real.

---

## Módulo 13

* IP: São voltáteis, containers nacsem e morrem com IPs novos. Decorar ou fixar IPs em arquivos de configuração é um anti-padrão grave.
* DNS embutido: O Docker possui um servidor DNS interno que transforma o nome do container em um histname válido

A resolução de nomes por hostname só funciona em redes customizadas (`docker network create`). Na rede padrão (`bridge`), os containers só conseguem se comunicar se usar o IP direto.

O banco de dados não deve expor as portas para a máquina host (`-p`) por questões de segurança (seguindo o principio do menor privilégio).
Como a API já está na mesma rede virtual e consegue acessa-lo diretamente pelo nome, abrir a porta 5432 para o mundo externo seria expor o cofre desnecessáriamente para a rede interna inteira ou para a internet (criando uma superfice de ataque vulneravel).
