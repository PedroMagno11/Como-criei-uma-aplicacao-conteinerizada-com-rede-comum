# Como criei uma aplicação conteinerizada com rede comum

Uma aplicação conteinerizada com rede comum se refere a uma configuração onde múltiplos contêineres (normalmente executados com Docker) são interconectados através de uma rede compartilhada, permitindo que se comuniquem entre si de forma transparente.

## Instruções

- Primeiro criei uma rede docker usando o seguinte comando: 

```bash
docker network create minha-rede
```

- Verifiquei se a rede foi criada:
```bash
docker network ls
```
Depois baixei a imagem do MySQL usando o seguinte comando: 
```bash
docker pull mysql
```
Em seguida iniciei o contêiner do MySQL na rede com o seguinte comando: 
```bash
docker run --name meu-mysql --network minha-rede -e MYSQL_ROOT_PASSWORD=minhasenha -v mysql-data:/var/lib/mysql -p 3306:3306 -d mysql
```

Iniciei também o contêiner da minha aplicação na mesma rede usando o seguinte comando: 
```bash
docker run --name minha-aplicacao --network minha-rede -p 8080:80 -v /caminho/para/minha/aplicacao:/var/www/html -d imagem-da-minha-aplicacao
```
Para testar essa conexão, testei diretamente no contêiner da minha aplicação, executando o seguinte comando para entrar no contêiner da minha aplicação:
```bash
docker exec -it minha-aplicacao bash
```
Agora, dentro do contêiner da minha aplicação, rodei o comando:
```bash
ping meu-mysql
```
---

**OBS:** Para você que assim como eu que ao rodar o comando `ping meu-mysql` recebeu a seguinte mensagem:
```bash
bash: ping: command not found
```

Para resolver isso fiz os seguintes passos:

 1- Entrei dentro do contêiner da minha aplicação.

 2- Executei o seguinte comando: 

    apt-get update && apt-get install -y iputils-ping



**OBS:** outra maneira de resolver isso é adicionando ao Dockerfile da sua aplicação o seguinte trecho: 
```dockerfile
RUN apt-get update && apt-get install -y iputils-ping
```
---

## Algo extra: 

Com o contêiner meu-mysql em execução, pude acessar ao MySQL, para acessar ao terminal desse contêiner, usei o seguinte comando: 
```bash
docker exec -it meu-mysql bash
```
Dentro do contêiner, executei: 
```bash
mysql -u root -p
```
### Alguns comandos básicos no MySQL 

Dentro do contêiner meu-mysql após ter executado o comando `mysql -u root -p` é possível executar comandos SQL. Vou citar alguns comandos:

- Listar todos os bancos de dados
```sql
SHOW DATABASES;
```
- Usar um banco de dados específico:
```sql
USE nome_do_banco;
```
- Criar um novo banco de dados:
```sql
CREATE DATABASE meu_banco_exemplo;
```
- Mostrar todas as tabelas em um banco de dados:
```sql
    SHOW TABLES;
```
- Ver estrutura da tabela:
```sql
    DESCRIBE nome_da_tabela;
```
- Sair do cliente MySQL:
```sql
    EXIT;
```

OBS: Comando para sair do terminal do contêiner: 
```bash
exit
```
---

### Um pouco sobre as configurações do container meu-mysql.

Configurações de variáveis (Opcional)

 - Criar um banco de dados automaticamente:
```bash
-e MYSQL_DATABASE=meu_banco
```
 - Criar um usuario adicional:
```bash
-e MYSQL_USER=usuario
-e MYSQL_PASSWORD=senha
```
Exemplo completo: 
```bash
    docker run --name meu-mysql -e MYSQL_ROOT_PASSWORD=minhasenha -e MYSQL_DATABASE=meu_banco -e MYSQL_USER=usuario -e MYSQL_PASSWORD=senha -p 3306:3306 -d mysql
```
---

## Autor
[Pedro Magno](https://github.com/PedroMagno11)