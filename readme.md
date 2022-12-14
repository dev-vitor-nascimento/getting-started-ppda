#  📝 COMO COMEÇAR - PLATAFORMA DE APPS

Este tutorial irá descrever como utilizar a Plataforma de Apps de Intelbras em 3 seções: Como criar um base app, Como criar um micro app e Comunicação entre micro apps e internacionalização.

Para utilizar a plataforma e criar projetos nela é necessário ter acesso ao grupo do GitLab [Plataforma Apps][link-plataforma-apps], uma vez com o acesso e as permissões necessárias, siga o tutorial.

---

## 🚀 Como criar um base app. 
Nesta seção será descrito como criar um base app para um novo projeto.

1. Crie um fork do [Base App Example][link-plataforma-apps].

    Lembre-se de dar ao fork o nome desejado para o novo projeto.

2. Faça o clone do fork para sua máquina.

3. Abra o projeto em uma IDE de sua preferência.

    Neste tutorial utilizaremos o  [Visual Studio Code][link-visual-studio-code].

4. Baixe as dependências do projeto.
    
    Para baixar as dependências utilize o seguinte comando: `flutter pub get`

5. Executar o app para verificar se ocorreu tudo bem.

    Em caso positivo, está tela deverá ser exbida.

    ![Base App Example](images/base_app_example.png)

    Nesse ponto você tem uma estrutura genêrica de base app, vamos para as personalizações.

6. No arquivo pubspec.yaml altere o atributo name para o nome desejado para o seu projeto.
   
    ![Project Name](images/project_name.png)

7. O passo anterior vai quebrar alguns imports do projeto, para consertar basta ir nos arquivos e substituir o antigo nome do projeto pelo novo.

    ![Folders](images/folders.png)

    ![imports](images/imports.png)

    Uma vez corrigidos, você terá um base app pronto para utilizar micro apps.

---

## 📱 Como criar um micro app.
Nesta seção será descrito como criar um novo micro app para a plataforma.

1. Abra um terminal.

2. Navegue até o diretório de sua preferencia.

3. Crie um pacote flutter. 
   
    Executando o comando: 
   
    ```console
    flutter create --template=package nome_do_micro_app
    ```

4. Abra o projeto em uma IDE de sua preferencia.

5. Adicione o Micro Core como dependencia do seu micro app.
   
	Para isso basta em pubspec.yml, na sessão dependencies, adicionar o seguinte trecho:

	```yml
    micro_core:
    	git:
      		url: https://deploy_token_micro_core_remp:tBAvrQN2SeJmrSBZSczi@git.intelbras.com.br/PlataformaApps/micro_core_remp.git
     		path: micro_core
      		ref: develop
    ```

6. Dentro da pasta lib crie uma classe chamada `MicroAppResolver`.
   
    Todo micro app deve ter uma classe `MicroAppResolver` que implementará a classe abstrata `MicroApp` declarada no Micro Core.

    ![Micro app resolver](images/micro_app_resolver.png)

    Certifique-se de implementar todos os getters necessários.

    - microAppName: deve retorar um string com o nome do micro_app.
    - injectionsRegister: deve retornar uma função responsável por criar as injeções de dependência.
    - createListener: deve retornar uma função responsável por registar os eventos que serão observados pelo micro app.
    - routes: deve retornar uma função responsável por retornar um Map de rotas nomeadas do micro app.

    Em micro apps novos podemos retornar funções ou maps vazios, uma vez um que o micro app ainda nao tem rotas, eventos ou injeção de dependencia, como visto na imagem acima.

    Uma vez com a classe implementada, você terá um micro app que pode ser utilizado por qualquer base app.

7.  Crie um página simples para testar o uso do seu micro app.

    ![Simple page](images/simple_page.png)

8.  Abra o `base app` em uma IDE.

9.  Adicione o micro app como dependência do seu base app:
	
    O exemplo abaixo é ilustrativo, use o nome do seu micro app e o path do micro app relativo ao base app.

	```yml
    micro_app_test:
    	path: ../micro_app_test
    ```

    Obs: Também é possivel importar micro apps de um repositório remoto, como o GitLab.
    ```yml
    micro_app_example:
        git:
            url: https://git.intelbras.com.br/PlataformaApps/micro_app_example.git
            ref: master
    ```

10. No arquivo `lib/app/core/micro_apps_resolvers.dart` do base app, adicione a classe `MicroAppResolver` do micro app recém criado à lista de micro apps resolvers.

    ![Micro app resolver](images/micro_app_resolvers.png)

11. No app_widget.dart do base app mude o atributo home para a página do seu micro app.

    ![App widget](images/app-widget.png)

12. Execute o base app.

13. Se tudo ocorreu bem, a página inicial do do base app será a página criada no micro app.

    Se a página criada for igual a recomendada no tutorial, a seguinte tela deverá ser exibida:

    ![App widget](images/my_page_execution.png)
    
14. Com este tutorial você conseguiu criar um micro app e utiliza-lo em um base app.

---

## 💡 Comunicação entre micro apps e internacionalização.

[link-plataforma-apps]: https://git.intelbras.com.br/PlataformaApps
[link-base-app-example]: https://git.intelbras.com.br/PlataformaApps/base_app_example
[link-visual-studio-code]: https://code.visualstudio.com/