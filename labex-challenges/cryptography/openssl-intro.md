# Introdução à Criptografia com OpenSSL (hands-on lab)
---

### 1- Criação de um arquivo de texto simples

Foi criado um arquivo chamado `secret.txt` contendo uma mensagem simples:

```bash
echo "LabEx has the best labs for fun, hands-on learning." > secret.txt
```

O conteúdo foi verificado com:

```bash
cat secret.txt
```

Nesse momento, o conteúdo ainda está totalmente legível e sem proteção.

---

### 2- Criptografia com OpenSSL

#### Conceitos importantes aprendidos:

* **enc** - modo de criptografia.
* **aes** - algoritmo de criptografia simétrica muito utilizado no mundo.
* **256** - tamanho da chave (mais seguro).
* **cbc** - modo de operação que aumenta a segurança dos blocos criptografados.
* **-salt** - adiciona aleatoriedade para aumentar a segurança.
* **-in** - arquivo original.
* **-out** - arquivo criptografado.
* **-pbkdf2** - método seguro para derivar chaves a partir de senhas.
* Importância de utilizar senhas fortes para proteção dos dados.

A mensagem foi criptografada utilizando o algoritmo aes-256-cbc:

```bash
openssl enc -aes-256-cbc -salt -in secret.txt -out secret.enc -pbkdf2
```

Após feito a criptografia:

* O arquivo `secret.enc` passa a existir.
* Seu conteúdo ficou ilegível quando visualizado com `cat`.

---

### 3- Descriptografia

A mensagem criptografada foi restaurada utilizando:

* **-d** - ativa o modo de descriptografia.

```bash
openssl enc -aes-256-cbc -d -in secret.enc -out decrypted.txt -pbkdf2
```

Foi necessário utilizar exatamente a mesma senha usada durante a criptografia.

A validação foi feita com:

```bash
cat decrypted.txt
```
A mensagem original reaparece normalmente.
E também utilizando:

```bash
diff secret.txt decrypted.txt
```

Sem saída no `diff` significa que os arquivos são idênticos e a descriptografia funcionou perfeitamente.

---

### 4- Gerenciamento de Chaves

O laboratório também demonstra a importância do gerenciamento seguro de senhas/chaves criptográficas.

Ao tentar descriptografar utilizando uma senha incorreta:
* O conteúdo gerado ficou ilegível.
* Comprova que a criptografia protege os dados corretamente.

---

#### Resumo:
Esta experiência prática introduz aos princípios fundamentais da criptografia que formam a base da segurança de dados moderna. 
Seja para proteção de dados pessoais ou desenvolvimento profissional, essas habilidades fornecem uma base sólida para futuras explorações em cibersegurança.


