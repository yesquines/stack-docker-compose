# Projeto Docker Compose

Objetivo do projeto é executar um ambiente interligando 2 aplicações:

* **app-python**: É uma página web que contém um formulario. Quando o formulário é submetido a aplicação enfilera a mensagem na fila do RabbitMQ
* **app-node**: É uma worker que consume da fila do RabbitMQ e armazena as mensagens no MySQL

### Solução com Docker Compose

A Solução foi implementada em 3 Etapas:

* Criação da Playbook para garantir as dependências (Docker e Docker-Compose)
* Criação dos Dockerfiles para as aplicações em NodeJS e Python.
* Criação do _docker-compose.yml_ 

Sendo assim, o Deploy da Solução pode ser realizado conforme as etapas a seguir:

1. **Instalação de Dependências**

As máquinas que receberão as aplicações precisam ter o _Docker_ e o  _Docker-Compose_ já instalados. Com isso, caso a máquina não esteja com essas dependências é necessário executar a playbook _requirements.yml_ utilizando o Ansible, conforme os comandos abaixo:

  * Instalação do Ansible ("Servidor" ou Local)

    - Família Debian
      ```bash
      sudo echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" | tee /etc/apt/sources.list
      sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
      sudo apt update 
      sudo apt install ansible -y
      ```

    - Família RedHat
      ```bash
      yum install epel-release -y
      yum install ansible -y
      ```

  * Execução da Playbook

    ```bash
    ansible-playbook -i 'ip_da_maquina,' requirements.yml
    ```

> Obs¹: Caso for mais de uma máquina é recomendado o uso do _inventário do Ansible_

> Obs²: Caso a execução for local, ou seja, na máquina que vai sustentar as aplicações, utilizar a opção `-c local` 

> Obs³: É recomendado que as máquinas sejam das Famílias Debian 10 ou RedHat 7

2. **Deploy da Aplicação**

Após a instalação das dependências, é possível realizar o Deploy executando os seguintes comandos na máquina que surportará as aplicações:

```bash
git clone https://github.com/yesquines/stack-docker-compose.git
cd stack-docker-compose/
docker-compose up --build -d
```
* Saída de Exemplo do comando:

  ```text
  ...
  Creating network "stack-docker-compose_default" with the default driver
  Creating stack-docker-compose_mysql_1    ... done
  Creating stack-docker-compose_rabbitmq_1 ... done
  Creating stack-docker-compose_app-python_1 ... done
  Creating stack-docker-compose_app-node_1   ... done
  ```

3. **Acesso a Aplicação**

Tanto a aplicação em NodeJS quanto a em Python, só ficam ativas a partir do momento que tiverem comunicação com o Rabbitmq ou com o MySQL.

Sendo assim, para validar o seu funcionamento, acesse a seguinte URL no navegador: **http://ip-da-maquina:8000/**

4. **Análise de Logs**

Para qualquer um dos serviços criados (app-node, app-python, rabbitmq e mysql) é possivel fazer a análise de logs da seguinte forma:

```
cd app-devops/
docker-compose logs <nome-do-serviço>
```

> Caso queira acompanhar os logs em tempo real utilize a opção `-f`

---
