#  📝 COMO COMEÇAR - PLATAFORMA DE APPS

Este tutorial irá descrever como utilizar a Plataforma de Apps de Intelbras em 4 seções:

1. Como criar um base app.
2. Como criar um micro app.
3. Comunicação entre micro apps.
4. Internacionalização de micro apps.

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
    
    Para baixar as dependências utilize o seguinte comando:
    ```console
    flutter pub get
    ```

5. Execute o app para verificar se ocorreu tudo bem.

    Em caso positivo, está tela deverá ser exbida:

    ![Base App Example](images/base_app_example.png)

    Nesse ponto você tem uma estrutura genêrica de base app, vamos para as personalizações.

6. No arquivo pubspec.yaml altere o atributo **name** para o nome desejado para o seu projeto.
   
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

6. Dentro da pasta lib crie uma classe chamada **MicroAppResolver**.
   
    Todo micro app deve ter uma classe **MicroAppResolver** que implementará a classe abstrata **MicroApp** declarada no Micro Core.

    ```dart
    import 'package:micro_core/app/micro_core_utils.dart';
    import 'package:micro_core/app/mircoapp.dart';

    class MicroAppResolver implements MicroApp {
        @override
        void Function() get createListener => () {};

        @override
        void Function() get injectionsRegister => () {};

        @override
        String get microAppName => "micro app teste";

        @override
        Map<String, WidgetBuilderArgs> get routes => {};
    }
    ```

    - microAppName: deve retorar um string com o nome do micro_app.
    - injectionsRegister: deve retornar uma função responsável por criar as injeções de dependência.
    - createListener: deve retornar uma função responsável por registar os eventos que serão observados pelo micro app.
    - routes: deve retornar uma função responsável por retornar um Map de rotas nomeadas do micro app.

    Em micro apps novos podemos retornar funções ou maps vazios, uma vez um que o micro app ainda nao tem rotas, eventos ou injeção de dependencia, como visto no exemplo acima.

    Uma vez com a classe implementada, você terá um micro app que pode ser utilizado por qualquer base app.

7.  Crie um página simples para testar o uso do seu micro app.

    ```dart
    import 'package:flutter/material.dart';

    class MyPage extends StatelessWidget {
        const MyPage({Key? key}) : super(key: key);

        @override
        Widget build(BuildContext context) {
            return Scaffold(
                appBar: AppBar(
                    title: const Text("Hello World"),
                ),
            );
        }
    }
    ```

8.  Abra o **base app** em uma IDE.

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

10. No arquivo **lib/app/core/micro_apps_resolvers.dart** do base app, adicione a classe **MicroAppResolver** do micro app recém criado à lista de micro apps resolvers.

    ```dart
    import 'package:micro_app_test/app/core/micro_app_resolver.dart' as micro_app_test;
    import 'package:micro_core/app/mircoapp.dart';

    class MicroAppResolvers {
        static List<MicroApp> getMicroAppsResolvers() {
            return [micro_app_test.MicroAppResolver()];
        }
    }
    ```

11. No app_widget.dart do base app mude o atributo **home** para a página do seu micro app.

    ![App widget](images/app-widget.png)

12. Execute o base app.

13. Se tudo ocorreu bem, a página inicial do do base app será a página criada no micro app.

    Se a página criada for igual a recomendada no tutorial, a seguinte tela deverá ser exibida:

    ![App widget](images/my_page_execution.png)
    
14. Com este tutorial você conseguiu criar um micro app e utiliza-lo em um base app.

---

## Utilizando micro apps já existentes na plataforma
Nesta seção será descrito com utilizar os micro apps da plataforma.

Para este tutorial iremos intregar um base app, o micro app criado na sessão anterior e o micro app de splash screen pertencente a plataforma

1. No micro app criado na sessão anterior, na pasta lib, cria um arquivo chamado redirect_pages.dart.
    
    Neste arquivo deve conter uma função que redireciona para a única página criada no micro app.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:micro_app_test/app/pages/my_page.dart';
    import 'package:micro_core/app/micro_core_utils.dart';

    redirectToMyPage() {
        Navigator.of(navigatorKey.currentContext!).push(
            MaterialPageRoute(
                builder: (context) => const MyPage(),
            ),
        );
    }
    ```

2. No base app, adicione o micro app de splash como dependência:
   
    ```yml
    micro_app_splash_terms:
        git:
            url: https://deploy_token_micro_app_splash_termofuse:oah4GA79yFuQHx5Wndss@git.intelbras.com.br/PlataformaApps/micro_app_splash_termofuse.git
            ref: base-app-example/develop
    ```

3. No arquivo lib/app/core/micro_apps_resolvers.dart do base, adicione o resolver do micro app de splash:
   
    ```dart
    import 'package:micro_app_test/app/core/micro_app_resolver.dart' as micro_app_test;
    import 'package:micro_app_splash_terms/app/core/micro_app_resolver.dart' as micro_app_splash;
    import 'package:micro_core/app/mircoapp.dart';

    class MicroAppResolvers {
        static List<MicroApp> getMicroAppsResolvers() {
            return [
                micro_app_test.MicroAppResolver(),
                micro_app_splash.MicroAppResolver()
            ];
        }
    }
    ```

4. O micro app de splash emite um evento que deve ser escutado pelo base app, para redirecionar para a próxima tela.
   
   No base App no arquivo lib/app/core/handler_router.dart adicione o seguinte trecho de código, para escutar o evento e fazer o redirecionamento para o micro app criado na sessão anterior:

   ```dart
    import 'package:micro_app_splash_terms/app/domain/events/event_finish_micro_app.dart';
    import 'package:micro_app_test/app/infra/redirect_pages.dart';
    import 'package:micro_core/app/event_bus/eventbus_implementation.dart';

    class HandlerRouter {
        static void startHandler() {
            EventBus.on<EventFinishMicroAppSplash>().listen((event) {
                redirectToMyPage();
            });
        }
    }
   ```

    o redirecionamento é feito com a função quer criamos no passo 1 desta sessão.

5. Execute o base app para testar, se tudo ocorreu bem deve ser exibida a tela de splash por 3 segudos, e logo após a tela do micro app criado na sessão anterior.

    Splash Screen:

    ![Splash Screen](images/splash_screen.png)

    Página micro app:

    ![App widget](images/my_page_execution.png)

---

## 💡 Comunicação entre micro apps.
Nesta seção será descrito como realizar a comunicação entre micro apps.

Para seguir com este tutorial é necessário que você já tenha conhecimento necessário para criar base app e micro apps.

1. Clone os seguintes repositórios:

    Base App Example:

    ```console
    git clone https://git.intelbras.com.br/PlataformaApps/base_app_example.git
    ```

    Micro App Example 1:

	```console
    git clone https://git.intelbras.com.br/PlataformaApps/micro_app_example.git
    ```

    Micro App Example 2:

	```console
    git clone https://git.intelbras.com.br/PlataformaApps/micro_app_example_2.git
    ```

2. No **base app** mude para a branch micro_app_example.
   
    Baixe todas as branches:
    ```console
    git fetch --all
    ```

    Mude de branch:
    ```console
    git checkout micro_app_example.
    ```

3. Baixe as dependências da nova branch
    ```console
    flutter pub get
    ```

4. Veja que o pubspec.yml do base app tem como dependência os dois micro apps que foram clonados.

    ![App widget](images/base_app_pubspec.png)

5. Além disso os resolvers do micro apps estão sendo registrados em lib/app/core/micro_apps_resolvers.dart.
    
    ![App widget](images/base_app_resolver.png)

6. Para entender como a comunicação acontece, vamos iniciar explorando o **micro app example 1**.

7. Observe em *lib/app/domain/events/event_show_other_micro_app.dart* um evento que é apenas uma classe vazia,
	esse evento será emitdo pelo **micro app example 1** quando se deseja mostrar um outro micro app, nesse caso **micro app example 2**.

    ![App widget](images/micro_app_event.png)

8. No arquivo *lib/app/infra/redirect_pages.dart* foi declarada um função de redirecionamento,
	sua responsabilidade é utilizar o *EventBus* presente no *micro core*, para emitir o evento que foi apresentado no passo anterior.

    ![App widget](images/micro_app_redirect_1.png)

9.  Na única página de temos neste micro app *lib/app/presentation/pages/page_example.dart* existe um botão, sua ação de clique é a função de redirecionamento.

    ![App widget](images/my_page_button.png)

10. Perceba que com o que sabemos até o momento, ao clicar no botão será executada uma função que emitirá um evento, e nada acontecerá pois ninguém está ouvindo este evento. Será mesmo?

11. Na verdade o **base app** está escutando este evento, vamos ver como acontece.

12. Agora no projeto do **base app**, especificamente em *lib/app/core/handler_router.dart*, observe que foi registrado a escuta de um evento, o mesmo que é emitido pelo botão do **micro app example 1**, obeserve o import do evento.

    ![App widget](images/base_app_handler_router.png)

13. Ao escutar este evento o **base app** está executando um função chamada redirectPageExample(),
	observe pelo import que esta função está registrada no **micro app example 2**.

    ![App widget](images/base_app_handler_router_2.png)

14. Agora no **micro app example 2** no arquivo *lib\app\infra\redirect_pages.dart* você poderá ver esta função e notar que ela executa um push para outra tela.

    ![App widget](images/micro_app_redirect_2.png)

15. Agora entendemos como um micro app consegue chamar outro sem sequer saber que ele existe.

16. Execute o base app e veja acontecer.

17. Revisando o fluxo de forma genérica: 
	- Um **micro app A** emite um evento.
	- A escuta deste evento deve estar registrada no **base app**.
	- Ao escutar este evento o **base app** irá executar um função de redirecionamento presente no **micro app B**.
	- Está função irá fazer um push de uma página no **micro app B**.

18. IMPORTANTE: Lembre-se, nenhum micro app sabe da existência de outro, toda e qualquer comunicação entre micro apps deve ser intermedia pelo base app, através de eventos.

---

## 🌐 Internacionalização de micro apps.
Nesta seção será descrito como internacionalizar micro apps.

**Caso tenha feito o tutorial da sesão anterior, pule para o passo 4**

1. Clone os seguintes repositórios:

    Base App Example:

    ```console
    git clone https://git.intelbras.com.br/PlataformaApps/base_app_example.git
    ```

    Micro App Example 1:

	```console
    git clone https://git.intelbras.com.br/PlataformaApps/micro_app_example.git
    ```

    Micro App Example 2:

	```console
    git clone https://git.intelbras.com.br/PlataformaApps/micro_app_example_2.git
    ```

2. No **base app** mude para a branch micro_app_example.
   
    Baixe todas as branches:
    ```console
    git fetch --all
    ```

    Mude de branch:
    ```console
    git checkout micro_app_example.
    ```

3. Baixe as dependências da nova branch
    ```console
    flutter pub get
    ```

4. No **base app** na pasta *lib/i18n* existem 3 arquivos de internacionalização.

    ![App widget](images/base_app_lib_i18n.png)

5. Estes arquivos são .json, onde as chaves são idênticas em todos os três arquivos, mas os valores variam de língua para língua, veja:
    
    en_US:

    ![App widget](images/en_US.png)

    es_ES:

    ![App widget](images/es_ES.png)

    pt_BR: 

    ![App widget](images/pt_BR.png)

6. Para utilizar as internacionalização os micro apps devem utilizar a dependência **i18n** presente na *micro commons dependencies* e as chaves dos arquivos de internacionalização.

7. Confira o import da micro commons dependencies no pubspec.yml de ambos micro apps clonados.

    ```yml
    micro_commons_dependencies_remp:
        git:
        url: https://deploy_token_micro_commons_dependencies_remp:JUPjHsZijQ86Ztz27G4y@git.intelbras.com.br/PlataformaApps/micro_commons_dependencies_remp.git
        ref: develop
    ```

8. Dessa forma se você deseja internacionalizar seu app, basta registar as chaves e valores nos arquivos de internacionalização do **base app** e utilizá-las nos micro apps por intermédio da **i18n**.

9. Veja os exemplos em ambos os micro apps no path *lib/app/presentation/pages*

    Micro App Example 1:
    
    ![App widget](images/micro_app_i18n_1.png)

    Micro App Example 2:

    ![App widget](images/micro_app_i18n_2.png)

10. Mude o idioma do emulador ou celular utilizado e execute base app para testar a internacionalização.

11. No exemplo dado neste tutorial são suportados os idiomas Inglês, Português e Espanhol.

12. Agora você está pronto para internacionalizar seus apps.

---

[link-plataforma-apps]: https://git.intelbras.com.br/PlataformaApps
[link-visual-studio-code]: https://code.visualstudio.com/
