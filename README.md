# **Merconnect**

Esta é a documensão relativa ao Merconnect, servidor da [Mercadapp](https://www.mercadapp.com.br/) que faz o gerenciamento de todos os supermercados cadastrados e se comunica com os aplicativos e sites.

### Stack
- Ruby on Rails (versão 4.2.10)
- Ruby (versão 2.5.7)
- Postgres 12.4

### APIs
As documentações das APIs do Merconnect estão disponíveis [aqui](https://web.postman.co/workspace/76123c49-6796-4902-8f73-17c43cecf9d7/overview?ctx=documentation).

### Style Guides

- Rails: [Robocop Rails Style Guide](https://github.com/rubocop-hq/rails-style-guide)
- Ruby: [Airbnb Ruby Style Guide](https://github.com/airbnb/ruby)
- Javascript: [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- CSS: [Airbnb CSS/Sass Style Guide](https://github.com/airbnb/css)

&nbsp;

&nbsp;

## **Primeiros Passos**

Para rodar a aplicação, é necessária que todas as 3 dependências da Stack estejam instaladas.

Logo após a instalação das dependências, recomendamos criar os bancos de dados que serão usados localmente. O tutorial para a instalação do Postgres e a criação dos bancos de dados `merconnect_development`e `merconnect_test` pode ser encontrado [aqui](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04-pt).

Após a criação dos bancos, clone o projeto para a sua máquina usando o comando na pasta em que deseja instalar o projeto:
```
git clone https://github.com/phxev/merconnect.git
```

Após a instalação do projeto, requisite a algum membro do time back-end o arquivo `.env`, este que possui todas as variáveis de ambiente necessárias para iniciar o projeto. Cole-o na raiz do projeto.

Como ainda não possuímos nenhum seed suficiente para testarmos toda a plataforma, utilizaremos um `dump.sql` (backup) do nosso banco de dados de produção. Peça a algum membro do time de back-end para provê-lo.

Coloque-o em uma pasta de fácil acesso, navegue pelo terminal até a pasta em que o arquivo `dump.sql` está localizado e rode o seguinte comando para restaurar o banco. A restauração pode demorar algumas horas.

```
pg_restore -d merconnect_development -h localhost -p 5432 -U nome_do_seu_usuário -n public --clean --no-owner -x --no-privileges --role=nome_do_seu_usuário_do_postgres --verbose dump.sql 
```

&nbsp;
&nbsp;

## **Testes**
Em todas as novas funcionalidades, o ideal é que sejam feitos os testes manuais e automatizados para os mesmos. Aqui citamos os testes básicos que devemos nos atentar ao implementarmos funcionalidades e refatorações no Merconnect.

### Testes automatizados

Para começar a rodar os testes, basta rodar o seguinte comando para criar o *database* de testes:
```
rake db:create db:structure:load db:migrate RAILS_ENV=test
```

Para rodar todos os testes, basta rodar o seguinte comando:
```
rake test
```

Também podemos rodar o teste para um único arquivo, exemplo:
```
rake test test/integration/batches_test.rb
```

### Testes manuais

Além dos testes automatizados, também é importante que nos atentemos para testes básicos para maximizar a boa experiência do usuário e evitar desentendimentos, abertura de chamados e inconsistências. 

Alguns exemplos de testes manuais que podem ser seguidos são:

1. Verificar se todo o **CRUD** está sendo feito com sucesso;
2. Verificar os campos obrigatórios dos formulários (required, *);
3. Verificar os valores mínimos e máximos dos formulários; 
4. Verificar se está claro para o cliente o que deve ser inserido no *form*. Caso contrário, melhorar a comunicação;
5. Verificar as transições de estado. Ex: Disparo de um email, mudança de status;
6. Vefiricar os inputs das funcionalidades/requests e o tratamento de erros para cada caso;
7. Verificar loops, se estão iniciando e encerrando corretamente;
8. Verificar possíveis configurações não ideais (fora do *Happy Path*).
9. Verificar se fluxo inteiro está correto e se está facilmente compreencível para o cliente. Caso necessite de informações adicionais, explicitá-las na tela.

### Testes em Background
Para testar funcionalidades processadas em segundo plano, é necessário rodar o Sidekiq usando o seguinte comando:
```
bundle exec sidekiq
```

Para acessar o painel do Sidekiq, basta acessar a raiz da aplicação e adicionar `/sidekiq` e estar logado como usuário Admin.
Caso alguma alteração seja feita dentro do código, é necessário reiniciar o *Sidekiq* localmente para que as alterações sejam efetivadas.

##### Batches
Para testar batches, é recomendado que baixe algum aquivo pré-existênte de batch [aqui](https://www.merconnect.com.br/batches). Depois basta acessar a rota `https://localhost:3000/markets/3/items/upload` e fazer o upload do arquivo `csv`, selecionando o tipo de batch que será processada.

##### Emails
Já sobre os emails, em ambiente de desenvolvimento estes serão abertos diretamente no seu navegador padrão. Já em ambiente de testes, a gem `letter_opener` irá redirecionar todos os emails para o email especificado pela ENV `EMAIL_INTERCEPTOR`, caso setado.

&nbsp;
&nbsp;

## **Revisões**
Possuimos um processo de revisão técnica das novas funcionalidades. Primeiramente o responsável pela tarefa deve fazer um *pull request* mirando para a `develop`, depois solicitar a revisão e assinalar, dentro do PR, dois desenvolvedores para fazer a revisão das mudanças. É recomendado que as pessoas assinaladas sejam as que tenham mais afinidade com as partes que foram adicionadas/alteradas e/ou fizeram alguma alteração no código em que tiveram as alterações (o próprio GitHub tem esse sistema de recomendação com base no histórico).

Ao criar o *Pull Request*, verifique se as mudanças necessitam de alguma label para que fique explícito para o revisor uma necessidade especial. São elas:

- **READY TO DEPLOY:** Revisada e pronta para deploy;
- **ENV:** Adição de uma variável de ambiente;
- **QUERY:** Necessidade de rodar uma query de *insert* ou *update* de dados;
- **STAGING:** Indica que a branch está no ambiente de testes;
- **STAND-BY:** Está bloqueada por algum motivo, não deve ser mergeada;
- **WARNING:** Necessita de uma revisão delicada, geralmente envolvendo 3 ou mais revisores;
- **WIP:** Work in progress. A branch ainda está com algumas alterações pendentes.

Após a revisão ser finalizada, sendo ela aprovada ou não, cabe ao revisor desassinalar ela mesma dos `Assignees` e assinalar o responsável pela branch que deve analisar os comentários feitos pelo revisor, caso houver. **O ciclo se mantêm até que os dois revisores do *Pull Request* tiverem aprovado as mudanças**.

Após a aprovação de ambos revisores, o *Pull Request* deve ser marcado com a label `READY TO DEPLOY` para assinalar que está pronta para ser mergeada na `develop`, juntamente com a movimentação do card no board do  [Jira](https://mercadapp.atlassian.net/jira/software/projects/MAPP/boards/1) para o campo **READY TO DEPLOY**.

&nbsp;
&nbsp;

## **Manual do Deploy**
Atualmente fazemos os deploys de novas funcionalidades todas as terças e quintas.
Antes de lançarmos as atualizações em produção, devemos seguir alguns passos:

### - Pré-deploy

1. Todas as branches com as alterações devem ser mergeadas na branch principal `develop` após serem **devidamente testadas**. Atualmente estamos fazendo um Pull Request da `develop` para a `master` para fazermos uma verificação manual de tudo o que irá subir;

2. Com todas as funcionalidades concentradas na branch `develop`, rodar todos os testes com o comando `rake test` e checar se todos rodaram com sucesso;

3. A branch `develop` deve ser enviada para o [Ambiente de Testes](https://merconnect-staging.herokuapp.com/), sendo necessário entrar nas telas dos pedidos e em locais em que houveram atualizações recentes com novas funcionalidades e/ou refatorações, por último fazendo um pedido pelo aplicativo de teste e verificar se o pedido foi feito com sucesso;

4. Verifique o [Heroku Status](https://status.heroku.com/) checando se os status de `Apps`, `Data` e `Tools` estão todos `OK` e verdes. Caso contrário, é recomendado que o deploy seja adiado para evitar problemas.

5. Caso a nova versão tiver migrations que alterem a estrutura já existente no Merconnect, é recomendado que seja feito um snapshot do banco. Para fazer isso, acesse a AWS [aqui](https://console.aws.amazon.com/rds/home?region=us-east-1#databases:), selecione o database `merconnect-production-db-r`, clique no botão `Actions` e selecione `Take snapshot`.

### - Deploying

1. Localmente, puxe as atualizações mais recentes das branches `develop` e `master` com o comando `git pull`, mudando de branch com o comando `git checkout nome_da_branch`;

2. Dentro da branch `master`, faça o merge da `develop` com o comando `git merge develop`.

3. Por último, suba a branch `master` para staging (se ainda não feito), e logo após para produção com o comando `git push production master`. Dependendo se tiver migrations grandes ou novos assets, o tempo de deploy poderá variar de 3 a 10 minutos. Acompanhar a execução do deploy até a sua conclusão;

4. Após o termino do deploy, aguardar o tempo de pré-boot (3-4 minutos) e prosseguir para os passos pós-deploy.

### - Pós-Deploy

1. **Fazer um pedido**. Entrar em algum app, fazer todo o fluxo normal do cliente e finalizar o pedido. Verificar se o pedido apareceu no Merconnect corretamente. Se possível, fazer o pedido tanto em um app iOS quanto no Android;
2. Caso houver alguma mudança envolvendo pagamento, testar o mesmo, principalmente se for no pagamento online;
3. Entrar no Merconnect App, ir na tela de pedidos, entrar em um pedido. Fazer a separação de um item;
4. Entrar na tela dos pedidos (index) de um mercado, depois entrar na tela de um pedido (show) e mudar o  status;
5. Testar todas as outras funcionalidades ou refatorações que foram feitas nessa atualização;
6. Fazer um push das alterações da branch `master` para o remoto ou mergear a develop com a master diretamente pelo GitHub;
7. Mover os cards que foram para produção para `DONE` no [Jira](https://mercadapp.atlassian.net/jira/software/projects/MAPP/boards/1).

### - Problemas no Deploy

Caso ocorra algum problema no deploy, é necessário fazer uma avaliação. Caso a correção seja simples, pode-se fazer na mesma hora sem muitos problemas, a menos que envolva os pedidos. Caso a correção seja complicada ou o problema não seja identificado, recomenda-se fazer o **rollback imediato** e adiar o deploy até a correção ser feita e testada novamente.
