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

==============================================================

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

================================================================

## Módulo 11

Desafio: Otimizacao e Seguranca de Imagens
Data: 01/07/26

#### 1. O que foi executado:

Comparacao de tamanho das imagens (docker images):
- meu-app:1.0 -> 1.02 GB (base cheia, com cache e lixo local)
- meu-app:2.0 -> 140 MB (base slim,s em cache do pip, filtrada)
Resultado: reducao drástica de tamanho

Validacao de privilégios (whoami):
- comando: `docker run --rm meu-app:1.0 whoami`
- retorno: root (vulnerável)

- comando: `docker run --rm meu-app:2.0 whoami`
- retorno: operario (usuario sem coroa, seguro)

#### 2. Conceitos chave absorvidos:

* `.dockerignore` impede que arquivos locais como `.git`, `__pycache__`e, principalmente, o `.env`entrem nas camadas da imagem Docker.
* Imagens base `:slim`trazem equilibrio para o mercado, evitando o peso da imagem cheia e as compatibilidades de compilacao da base `alpine`.
* `--no-cache-dir`no pip evita o acumulo de arquivos temporarios de download dentro da camada do container.
* O principio do menor privilégio: isntalar os pacotes como `root`e rebaixar o usuario para `operario`no final do Dockerfile protege o host caso o container seja invadido.

#### 3. A Diferenca entre `.gitignore` e `.dockerignore`:
* `.gitignore`: diz ao git o que não salvar no historico e não subir para o GitHub (protege o código na nuvem).
* `.dockerignore`: diz ao Docker o que não incluir no build do container. (protege o ambiente de producão contra vazamento de segredos(.env) e imagens infladas).
