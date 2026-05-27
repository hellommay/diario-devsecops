# Parte teórica de Aprendizado

## 1 - Simulação de Ataque de força bruta e Segurança de Autentiçacão

Tentativas manuais de login utilizando credenciais comuns como (test/password123 e admin/admin) a fim de analisar o comportamemnto de segurança de um sistema de autenticação.

#### Conceitos abordados
* **Mensagem de Erro genérica:** O sistema responde a falhas com a mensagem genérica gerada pelo navegador "Invalid username or password". Impedindo que o atacante saiba se o erro foi no nome de usuário ou na senha.
* **Ausência de bloqueios no laboratório:** Diferente dos ambientes de produção, o ambiente de teste permite tentativas ilimitadas. Facilitando a demonstração de ataques automatizados.
* **Ataque de força bruta:** O processo manual realizado simula a lógica de um ataque de força bruta. Em cenários reais, os invasores utilizam ferramentas automatizadas capazes de testar milhares de combinações por segundo.

---
## 2 - Examinando a lista de Senhas

Transição de um ataque de força bruta manual para uma abordagem mais eficiente e automatizada, utilizando listas de senhas pré-compiladas.

#### Uso de Wordlists 
* **Ataques reais:** Hackers utilizam listas gigantescas com milhões de senhas vazadas de incidentes de segurança passados.

No laboratório é utilizada uma lista reduzida contendo as senhas fracas mais comuns da internet, estava localizada na ``` /home/labex/project/500-worst-passwords.txt ```.
Para examinar o conteúdo do arquivo usamos o comando *head*, que mostra as primeiras linhas de um arquivo : ```head -n 10 500-worst-passwords.txt ```.

#### Conceito chave: Ataque de Dicionário
* **Previsibilidade:** As senhas mais comuns geralmente são sequências numéricas ou palavras simples.
* **Força bruta vs Dicionário:** Força bruta testa combina
* **Força Bruta vs Dicionário:** A Força Bruta testa combinações aleatórias de caracteres, o Ataque de dicionário foca em testar primeiro as senhas com maior probabilidade de sucesso. Reduzindo drasticamente o tempo necessário para invadir um sistema.

---
## 3- Configurando e Executando o Hydra para Ataques de Força  Bruta

####  Configuração e Preparação dos arquivos
Antes de iniciar um ataque, é necessário preparar as listas de alvos (usuários e senhas):

* **Nomes de usuário:** Criação de um arquivo chamado ```usernames.txt``` contendo credenciais padrão possiveis (admin, user, root)
* **Comandos de verificação:** Uso do comando ```cd``` para navegar até o diretório do projeto e uso do ```cat``` para verificar a criação correta da lista.
* **Ajuda do Hydra:** O comando ```hydra -h``` exibe o menu de ajuda com os protocolos suportados (HTTP,FTP,SSH, etc.) e opções de personalização.

Para automatizar o teste contra uma página de login local, é usado o seguinte comando: 
``` hydra -L ~/project/usernames.txt -P ~/project/500-worst-passwords.txt localhost -s 8080 http-post-form "./:username=^USESR^&password=^PASS^:Invalid username or password" -o ~/projejct/hydra_results.txt ```

* -L: Especifica o arquivo com a lista de usuários.
* -P: Especifica o arquivo com a lista de senhas.
* localhost -s 8080: Define o alvo (servidor local) e a porta de comunicação (8080).
* http-post-form: O protocolo usado para interagir com formulários web que enviam dados via método POST.
* /:username=^USESR^&password=^PASS^:Invalid username or password": 
**/** - A rota da página de login. 
**username=^USESR^&password=^PASS^** - Os nomes exatos dos campos do formulário onde o Hydra vai injetar os dados.
**:Invalid username or password** - A mensagem de erro que o Hydra deve procurar para saber que a tentativa falhou. Se por acaso a mensagem mudar significa que deu certo.
* -o: Salva os resultados positivos (credenciais encontradas) em um arquivo de texto.

#### Análise dos Resultados 
Após a execução, os logins válidos são exibidos no formato (login: [usuario] password: [senha] found.)

O Hydra testa centenas de combinações em minutos. O sucesso do teste comprova que senhas comuns e previsiveis são facilmente comprometidas por ferramentas automatizadas como esta.
Para conter esse tipo de ameaça, profissionais de segurança reforçam a obrigatoriedade de senhas complexas e, principalmente, a implementação de politifcas de bloqueio de conta após sucessivas tentativas incorretas.

--- 
# Parte de desafio prático (hands-on) 

## Execução do Ataque com Hydra

Objetivo: Simular um ataque de força bruta aautomatizado contra a pagina de login local utilizando listas de credenciais previamente configuradas.

Passo 1: Execução do comando
O ataque foi iniciado no terminal através do comando consolidado do Hydra.

Passo 2: Monitoramento e Coleta
A ferramenta testou as cobinações cruuzando os arquivos ```usernames.txt``` e ```500-worst-passwords.txt```.
O Progresso foi exibido em tempo real na tela do terminal.

Passo 3: Verificação do Sucesso
Após a finalização, os resultados foram validados no arquivo de saída ```hydra_results.txt```. As credenciais vulneráveis extraídas seguiram o padrão de sucesso da ferramenta (login: [username] password: [senha] found).


