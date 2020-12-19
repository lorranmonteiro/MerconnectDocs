# **Merconnect**

Esta é a documensão relativa ao Merconnect, servidor da [Mercadapp](https://www.mercadapp.com.br/) que faz o gerenciamento de todas os supermercados cadastrados e se comunica com os aplicativos e sites.

A documentação da API do Merconnect está disponível [aqui](https://www.postman.com/api-documentation-tool/).

#### Stack
- Ruby on Rails (versão 4.2.10)
- Ruby (versão 2.5.7)
- PostgreSQL 12.

#### Style Guides

- Rails: [Robocop Rails Style Guide](https://github.com/rubocop-hq/rails-style-guide)
- Ruby: [Airbnb Ruby Style Guide](https://github.com/airbnb/ruby)
- Javascript: [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- CSS: [Airbnb CSS/Sass Style Guide](https://github.com/airbnb/css)

## Primeiros Passos

Para rodar a aplicação, é necessária que todas as 3 dependências da Stack estejam instaladas.

Logo após a instalação das dependências, recomendamos criar os bancos de dados que serão usados localmente. O tutorial para a instalação do Postgres e a criação dos bancos de dados `merconnect_development`e `merconnect_test` pode ser encontrado [aqui](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04-pt).

Após a criação dos bancos, clone o projeto para a sua máquina usando o comando na pasta em que deseja instalar o projeto:
```
git clone https://github.com/phxev/merconnect.git
```

Após a instalação do projeto, requisite a algum membro do time back-end o arquivo `.env`, este que possui todas as variáveis de ambiente necessárias para iniciar o projeto. Cole-o na raiz do projeto.

Como ainda não possuímos nenhum seed suficiente para testarmos toda a plataforma, utilizaremos um `dump.sql` (espécie de backup) do nosso banco de dados de produção. Peça a algum membro do time de back-end para provê-lo.

Coloque-o em uma pasta de fácil acesso, navegue pelo terminal até a pasta em que o arquivo `dump.sql` está localizado e rode o seguinte comando para restaurar o banco. A restauração pode demorar algumas horas.

```
pg_restore -d merconnect_development -h localhost -p 5432 -U nome_do_seu_usuário -n public --clean --no-owner -x --no-privileges --role=nome_do_seu_usuário_do_postgres --verbose dump.sql 
```

## Testes

##### Emails
In development and staging, emails are not supposed to be actually sent.
In development, the `letter_opener` gem will make all emails pop up in your default browser.
In other modes, the `recipient_interceptor` gem will redirect all emails to the address specified by the `EMAIL_INTERCEPTOR` env if it's set.

## Revisões

## Deploy

# Merconnect


This is Merconnect, the web platform to manage markets. These are some useful information to make future development easier.

# API #

Check out the API documentation for the mobile apps by accessing the url `/api/docs` while logged in as an admin user.

# Stack #

- Ruby 2.5.7
- Rails 4.2.10
- PostgreSQL
- Sidekiq

# Setup #

After cloning this repository, setup your environment by running:

```
bin/setup your_admin_password
```

This will install system dependencies, bundle gems, prepare the database and create a new admin user with the password you set. You may change the script in `bin/setup`. It assumes you are running on OSX.

A complete .env file is required. You have to request it to one of the programmers. Otherwise the app will not run.

**The seeds.rb are not complete, so you will probably need a copy of the database to work with. Ask the administrator for one user able to make a dump.**

# Deploy #

The application is running on Heroku with GitHub integration.

- Every commit to `master` will deploy to `merconnect-production`.

You have two options for deploying to production. Open Heroku then:

1. Find the `merconnect` application and deploy from master manually.
2. Find the Merconnect pipeline and promote the `merconnect-staging` application to production manually.

Prefer method 1 because it's configured to run migrations automatically.
Use method 2 when there are no migrations to run because it deploys faster.

# Development #

Run rails server:

```
rails s
```

## Sidekiq ##

Sidekiq is mandatory in production and it's already set up on Heroku.
It handles async operations on Rails applications, like sending emails and processing files.
Sidekiq requires Redis (see "Setup" above).
You may need to run Redis and Sidekiq in development if you are developing async features.

Start Redis:

```
redis-server
```

Then start Sidekiq:

```
bundle exec sidekiq
```

You can see the Sidekiq UI at `/sidekiq` while logged in as an admin user.

## Foreman ##

Optionally, you can use Foreman to run both rails server and sidekiq.
This gem works similarly to how Heroku runs the application in production.

Start redis:

```
redis-server
```

Then start rails server and sidekiq:

```
foreman start
```

## Emails ##

In development and staging, emails are not supposed to be actually sent.
In development, the `letter_opener` gem will make all emails pop up in your default browser.
In other modes, the `recipient_interceptor` gem will redirect all emails to the address specified by the `EMAIL_INTERCEPTOR` environment variable if it's set.

## Annotate Models ##

Display commented attributes at the top of model files.
Update the attributes by executing a migration or running:

```
bundle exec annotate
```
