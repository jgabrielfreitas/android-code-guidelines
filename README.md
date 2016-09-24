# 1. Project guidelines

## 1.1 Project structure

Novos projetos devem seguir a estrutura do projeto Gradle Android que está definido no guia do [usuário do plugin Gradle Android](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Project-Structure). O projeto [ribot Boilerplate](https://github.com/ribot/android-boilerplate) é uma boa referência para começar.

## 1.2 Nome de arquivos

### 1.2.1 Arquivos `.class`
Nomes de classes devem ser escritas no formato [UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase).

Para classes que herdam (`extends`) de um componente do Android, deve terminar com o nome do componente;
for example: `SignInActivity`, `SignInFragment`, `ImageUploaderService`, `ChangePasswordDialog` ...

### 1.2.2 Resources

Resources devem conter o formato __lowercase_underscore__ em seus nomes.

#### 1.2.2.1 Drawables

Convenções para nomear arquivos drawables:


| Tipo         | Prefixo           |		Exemplo                  |
|--------------| ------------------|-----------------------------|
| Action bar   | `ab_`             | `ab_stacked.9.png`          |
| Button       | `btn_`	           | `btn_send_pressed.9.png`    |
| Dialog       | `dialog_`         | `dialog_top.9.png`          |
| Divider      | `divider_`        | `divider_horizontal.9.png`  |
| Icon         | `ic_`	           | `ic_star.png`               |
| Menu         | `menu_	`          | `menu_submenu_bg.9.png`     |
| Notification | `notification_`	 | `notification_bg.9.png`     |
| Tabs         | `tab_`            | `tab_pressed.9.png`         |

Convenções para nomear ícones (seguir a [iconography guidelines](http://developer.android.com/design/style/iconography.html)):

| Asset Type                      | Prefixo            | Example                      |
| --------------------------------| ----------------   | ---------------------------- |
| Icons                           | `ic_`              | `ic_star.png`                |
| Launcher icons                  | `ic_launcher`      | `ic_launcher_calendar.png`   |
| Menu icons and Action Bar icons | `ic_menu`          | `ic_menu_archive.png`        |
| Status bar icons                | `ic_stat_notify`   | `ic_stat_notify_msg.png`     |
| Tab icons                       | `ic_tab`           | `ic_tab_recent.png`          |
| Dialog icons                    | `ic_dialog`        | `ic_dialog_info.png`         |

Convenção para nomear estados de `selectors`:

| Tipo         | Sufixo          |		Exemplo                  |
|--------------|-----------------|-----------------------------|
| Normal       | `_normal`       | `btn_order_normal.9.png`    |
| Pressed      | `_pressed`      | `btn_order_pressed.9.png`   |
| Focused      | `_focused`      | `btn_order_focused.9.png`   |
| Disabled     | `_disabled`     | `btn_order_disabled.9.png`  |
| Selected     | `_selected`     | `btn_order_selected.9.png`  |


#### 1.2.2.2 Layout

Arquivos de layout deve coincidir com o nome dos componentes do Android que estão destinados, com o prefixo sendo o nome de seu componente.
Por exemplo, se criarmos um layout para a `SingInActivity`, o seu nome será `activity_sign_in.xml`.


| Componente       | Nome da classe         | Nome do layout                |
| ---------------- | ---------------------- | ----------------------------- |
| Activity         | `UserProfileActivity`  | `activity_user_profile.xml`   |
| Fragment         | `SignUpFragment`       | `fragment_sign_up.xml`        |
| Dialog           | `ChangePasswordDialog` | `dialog_change_password.xml`  |
| AdapterView item | ---                    | `item_person.xml`             |
| Partial layout   | ---                    | `partial_stats_bar.xml`       |

Um caso diferente é quando criamos um layout que vai ser inflado por um `adapter`, para preencher um `ListView`, por exemplo.
Neste caso, o nome do layout deve começar com `item_`.

Em alguns casos, estas regras não poderão ser aplicadas. Por exemplo, ao criar arquivos de layout que se destinam a fazer parte de outros layouts. Neste caso, você deve usar o prefixo `partial_`.

#### 1.2.2.3 Menu

Similar aos arquivos de layout, os arquivos de menu devem conter o nome do componente. Se definirmos um menu que será usado na classe `UserActivity`, seu nome será `activity_user.xml`.

Uma boa prática é *não* incluir a palavra `menu` como prefixo do arquivo, porque ele já se encontra na pasta `menu` do seu projeto e nas referências (R.`menu`.menu_activity_user).

#### 1.2.2.4 Values

Arquivos da pasta `values` devem estar no __plural__, ex. `strings.xml`, `styles.xml`, `colors.xml`, `dimens.xml`, `attrs.xml`

---

# 2 Code guidelines

## 2.1 Java

### 2.1.1 Não ignore as exceptions

Você **nunca** deverá fazer da seguinte forma:

```java
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) { }
}
```

_Você pode pensar que o seu código nunca vai estourar exception ou que não é importante trata-la. Ignorando exceções como foi mostrado acima, cria minas em seu código para alguém tropeçar algum dia. Você deve tratar as exceptions com a sua própria peculiaridade. O tratamento específico varia dependendo do caso._ - ([Android code style guidelines](https://source.android.com/source/code-style.html))

**Altarnativas que aceitas:**

+ Retorne a exception que o seu método pode retornar em casos de erro
```java
void setServerPort(String value) throws NumberFormatException {
    this.serverPort = Integer.parseInt(value);
}
```

+ Retorne a exception que é apropriada para a sua camada de abstração
```java
void setServerPort(String value) throws ConfigurationException {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        throw new ConfigurationException("Port " + value + " is not valid.");
    }
}
```

+ Trate o erro e substitua por uma solução apropriada no bloco catch
```java
/** Set port. If value is not a valid number, 80 is substituted. */

void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        serverPort = 80;  // default port for server
    }
}
```

Você pode ver outras soluções alternativas [aqui](https://source.android.com/source/code-style.html#dont-ignore-exceptions).

### 2.1.2 Não trate exceptions como genéricas

Você **não** pode fazer isso (sério, não faça, por favor):

```java
try {
    someComplicatedIOFunction();        // IOException
    someComplicatedParsingFunction();   // ParsingException
    someComplicatedSecurityFunction();  // SecurityException
    // phew, made it all the way
} catch (Exception e) {                 // Aqui capturamos uma exception genérica
    handleError();                      // com um tratamento genérico!
}
```

Veja mais detalhes sobre esse assunto [aqui](https://source.android.com/source/code-style.html#dont-catch-generic-exception).

### 2.1.3 Não use `finally`

_Nós não usamos `finally`. Não há garantias de quando um finalizador será chamado, ou mesmo que ele será chamado em tudo. Na maioria dos casos, você pode fazer o que você precisa de um finalizador com boa manipulação de exceção. Se você **realmente** precisar, defina um método `close()` (ou semelhante) e um comentário dizendo exatamente quando esse método precisa ser chamado._ - ([Android code style guidelines](https://source.android.com/source/code-style.html#dont-use-finalizers))


### 2.1.4 Imports

Quando você quiser usar a classe `Bar` do pacote `foo`, há duas maneiras possíveis de importá-lo

A ruim: `import foo.*;`
+ Potencialmente reduz o número de declarações de importação.

A boa: `import foo.Bar;`
+ Torna óbvio que as classes são realmente utilizados e o código se torna mais legível para futuras manutenções.

Use `import foo.Bar;` quando se tratar de código para Android. As únicas exceções que essa regra não se aplica é no uso de bibliotecas do Java (ex. `java.util.*`, `java.io.*`) e para testes unitários (`junit.framework.*`).

## 2.2 Regras da codificação Java

### 2.2.1 Definições de atributos e nomes

Atributos **devem** ser declarados __no topo da classe__ e eles **devem** seguir as seguintes lista de regras:

* Nunca utilizar [Hungarian notation](https://en.wikipedia.org/wiki/Hungarian_notation) - isso é muito feio
* Atributos `static` ou `final` devem estar TODOS_EM_CAPS_COM_UNDERSCORES
* Outros atributos devem estar escritos com __lower case__
* Todos os atributos devem estar no padrão [CamelCase](https://pt.wikipedia.org/wiki/CamelCase)

Exemplo:

```java
public class MyClass {
    public static final int SOME_CONSTANT = 42;
    public int publicField;
    private static MyClass MY_CLASS;
    int packagePrivate;
}
```

### 2.2.3 Tratar siglas como palavras

| Bom :+1:         | Ruim :-1:        |
| ---------------- | ---------------- |
| `XmlHttpRequest` | `XMLHTTPRequest` |
| `getCustomerId`  | `getCustomerID`  |
| `String url`     | `String URL`     |
| `long id`        | `long ID`        |

### 2.2.4 Use TAB para indentação

Use __TAB__ para indentação de blocos:

```java
if (x == 1) {
    x++;
}
```

### 2.2.5 Sobre usar `{ }`

> Lembre-se sempre de verificar se há espaços entre os parenteses, chaves e comandos

Se a condição e o corpo cabem em apenas uma linha, _não utilize chaves_.

Isso é __bom__:
```java
if (condition) body();
```

Isso é __ruim__:
```java
if (condition)
    body();  // nooooooo!!
```

### 2.2.6 Annotations

#### 2.2.6.1 Práticas de `Annotations`

De acordo com o guia de estilo de código do Android,
According to the Android code style guide, as práticas padrão para algumas das anotações predefinidos em Java são:

* `@Deprecated`: A anotação @Deprecated __deve ser usada__ sempre que um método ou atribudo está descontinuado. Você também deverá criar um comentário sobre o método anotado informando qual método substitui o mesmo. Lembrando que o método anotado com `@Deprecated` deve continuar funcionando e desligado em futuras manutenções.

* `@Override`: A anotação @Override __deve ser usada__ sempre que um método substitui a declaração ou a aplicação de uma super-classe.

* `@SuppressWarnings`: A anotação @SuppressWarnings só deve ser usado em circunstâncias em que é impossível de eliminar um aviso. Se um aviso este passa a ser "impossível de eliminar" de teste, a anotação @SuppressWarnings deve ser usado, de modo a assegurar que todos os avisos refletir problemas reais do código.

Mais informações sobre `Annotations` podem ser encontradas [aqui](http://source.android.com/source/code-style.html#use-standard-java-annotations).

#### 2.2.6.2 Estilos de Annotations

__Classes, Métodos e Contrutores__

Quando as anotações são aplicadas a uma classe, método ou construtor, eles são listados após o bloco de documentação e deve aparecer como __uma anotação por linha__.

```java
/* Exemplo de bloco para classe */
@AnnotationA
@AnnotationB
public class MyAnnotatedClass { }
```

__Atributos__

Anotações aplicadas em atributos devem estar listados na __mesma linha__.

```java
@Nullable @Mock DataManager dataManager;
```

### 2.2.7 Limitar o escopo de variáveis

_O escopo de variáveis ​​locais devem ser mantidos a um mínimo (Effective Java Item 29). Ao fazer isso, você aumenta a legibilidade e manutenção de seu código e reduzir a probabilidade de erro. Cada variável deve ser declarada no bloco mais interno que inclui todos os usos da variável._

_As variáveis ​​locais devem ser declaradas no momento da sua primeira utilização. Quase todas as declarações de variáveis locais devem conter um inicializador. Se você ainda não tem informações suficientes para inicializar uma variável de forma sensata, você deve adiar a declaração até que tenha as informações necessárias para isso._ - ([Android code style guidelines](https://source.android.com/source/code-style.html#limit-variable-scope))

### 2.2.8 Outro padrão de import

Se você estiver usando uma IDE como o Android Studio, você não precisa se preocupar com isso porque a sua IDE já está obedecendo estas regras. Se não, dê uma olhada abaixo.

A ordenação das declarações de importação é:

1. Android imports
2. Imports de terceiros (com, junit, net, org)
3. java e javax
4. Imports do mesmo projeto

Para coincidir exatamente com as configurações da IDE, as importações devem ser:

* Ordenados alfabeticamente dentro de cada grupo, com letras maiúsculas antes de letras minúsculas (por exemplo, Z antes de a).
* Deve haver uma linha em branco entre cada grande agrupamento (android, com, JUnit, net, org, java, javax).

Mais informações [aqui](https://source.android.com/source/code-style.html#limit-variable-scope)

### 2.2.9 Log

Use os métodos de registro fornecidos pela classe `Log` para imprimir mensagens de erro ou outras informações que possam ser úteis para os desenvolvedores identificarem os problemas:

* `Log.v(String tag, String msg)` (verbose)
* `Log.d(String tag, String msg)` (debug)
* `Log.i(String tag, String msg)` (informações)
* `Log.w(String tag, String msg)` (alertas)
* `Log.e(String tag, String msg)` (erros)

Como regra geral, use o nome da classe como tag e definimos como um campo `static final` na parte superior do arquivo. Por exemplo:

```java
public class MyClass {
    private static final String TAG = MyClass.class.getSimpleName();

    public myMethod() {
        Log.e(TAG, "oh no, a wild error appears");
    }
}
```

VERBOSE e DEBUG logs __devem__ estar desabilitador nas builds de produção. Também é recomendado desativar INFORMATION, WARNING e logs de ERROR, mas você pode querer mantê-los ativado se você acha que eles podem ser úteis para identificar problemas em builds de produção. Se você decidir deixá-los habilitado, você tem que ter certeza que eles não estão vazando informações privadas, como endereços de e-mail, IDs de usuário, etc.

Para mostrar somente logs em builds de DEBUG:

```java
if (BuildConfig.DEBUG) Log.d(TAG, "The value of x is " + x);
```

### 2.2.10 Ordenação do `.class`

Não há uma solução correta única para isso, mas usando uma ordem __lógica__ e __consistente__ irá melhorar a capacidade de aprendizado de código e legibilidade. É recomendado usar a seguinte ordem:

1. Atributos
2. Construtores
3. Métodos anotados com `@Override` e callbacks (public ou private)
4. Métodos públicos
5. Métodos privados
6. Métodos de interfaces

Exemplo:

```java
public class MainActivity extends Activity {

	  private String title;
    private TextView titleTextView;

    public void setTitle(String title) {
    	this.title = title;
    }

    @Override
    public void onCreate() {
        ...
    }

    private void setUpView() {
        ...
    }

}
```

Se sua classe está herdando de um __componente do Android__, como uma `Activity` ou `Fragment`, é uma boa prática ordenar os métodos de substituição para que eles siguam o __ciclo de vida do componente__. Por exemplo, se você tem uma `Activity` que implementa `onCreate()`, `onDestroy()`, `onPause()` e `onResume()`, a forma correta de ordenar será:

```java
public class MainActivity extends Activity {

	  // Ordenando de acordo com o ciclo de vida da Activity
    @Override
    public void onCreate() {}

    @Override
    public void onResume() {}

    @Override
    public void onPause() {}

    @Override
    public void onDestroy() {}

}
```

### 2.2.11 Ordenação de parâmetros em métodos

Ao programar para o Android, é bastante comum para definir os métodos que levem um `Context` como parâmetro. Se você estiver escrevendo um método como este, o __Context__ deve ser o __primeiro__ parâmetro.

No caso de __callbacks__, devemos utilizar o oposto, sendo elas o __último__ parâmetro.

Exemplos:

```java
// Context em primeiro
public User loadUser(Context context, int userId);

// Callbacks como último
public void loadUserAsync(Context context, int userId, UserCallback callback);
```

### 2.2.13 Strings, nomes e valores

Muito dos elementos da SDK do Android como `SharedPreferences`, `Bundle`, ou `Intent` usam uma abordagem de chave-valor, por isso é muito provável que, mesmo para um pequeno aplicativo que você acaba tendo que escrever um monte de constantes String.

Ao usar um desses componentes, é __necessário definir__ as chaves como um campos `static final` e devem conter o prefixo, conforme indicado abaixo.

| Elemento           | Prefixo             |
| -----------------  | ------------------- |
| SharedPreferences  | `SHARED_`           |
| Bundle             | `BUNDLE_`           |
| Fragment Arguments | `ARGUMENT_`         |
| Intent Extra       | `EXTRA_`            |
| Intent Action      | `ACTION_`           |

Note que os argumentos de um `Fragment` - `Fragment.getArguments ()` - também são um Bundle. No entanto, porque este é um uso bastante comum de Bundles, nós definimos um prefixo diferente para eles.

Exemplo:

```java
// Note que o valor do campo é o mesmo que o nome para evitar problemas de duplicação
static final String SHARED_EMAIL = "SHARED_EMAIL";
static final String BUNDLE_AGE   = "BUNDLE_AGE";
static final String ARGUMENT_USER_ID = "ARGUMENT_USER_ID";

// itens relacionados com Intent devem utilizar nome completo do pacote como valor
static final String EXTRA_SURNAME = "com.myapp.extras.EXTRA_SURNAME";
static final String ACTION_OPEN_USER = "com.myapp.action.ACTION_OPEN_USER";
```

### 2.2.14 Argumentos em Fragments e Activities

Quando os dados são passados ​​para um `Activity` or `Fragment` através de uma `Intent` ou um `Bundle`, as chaves para os diferentes valores __devem__ seguir as regras descritas na seção acima.

Quando uma `Activity` ou `Fragment` esperam argumentos, deve-se fornecer um método `public static` que facilita a criação da `Intent` ou `Fragment`.

No caso de Activities, o método se chamará `getStartIntent()`:

```java
public static Intent getStartIntent(Context context, User user) {
	Intent intent = new Intent(context, ThisActivity.class);
	intent.putParcelableExtra(EXTRA_USER, user);
	return intent;
}
```

Para `Fragments` é denominada `newInstance()` e processa a criação da `Fragment` com os argumentos certos:

```java
public static UserFragment newInstance(User user) {
	UserFragment fragment = new UserFragment;
	Bundle args = new Bundle();
	args.putParcelable(ARGUMENT_USER, user);
	fragment.setArguments(args)
	return fragment;
}
```

__Nota 1__: Estes métodos devem estar no topo da classe, antes do `onCreate()`.

__Nota 2__: Se nós fornecemos os métodos descritos acima, as chaves para extras e argumentos devem ser `private` porque não há necessidade de que eles sejam expostos para outras classes.

### 2.2.15 Tamanho máximo da linha

As linhas não podem exceder os __100 caracteres__.
Se o seu código exceder esse limite por algum motivo, você terá duas opções:

* Extrair para uma variável local ou método (recomendado);
* Aplicar quebras de linhas para dividir uma única linha em várias

Existem duas __exceções__ quando a sua linha pode exceder o limite de caracteres:

* Linhas que não possíveis de quebrar, ex. uma URL
* Padrão de `package` e `import`

#### 2.2.15.1 Quebra de linha

Não existe uma fórmula exata que explica como quebra de linha e muitas vezes as soluções diferentes são válidas.
No entanto, existem algumas regras que podem ser aplicadas a casos comuns.

__Operadores__

Quando a linha é quebra um operador, a linha vem __antes__ do operador.
Ex:

```java
int longName = anotherVeryLongVariable + anEvenLongerOne - thisRidiculousLongOne
        + theFinalOne;
```

Se possível, quebre a operação em partes.
Ex:
```java
int foo = anotherVeryLongVariable + anEvenLongerOne;
int bar = foo - thisRidiculousLongOne;
int longName =  bar + theFinalOne;
```
> Desta forma, você ganha uma certa precisão no debug, caso tenha problemas com o algoritmo.

__Em caso de métodos__


Quando múltiplos métodos estão encadeados na mesma linha (ex. o padrão [Builder](https://pt.wikipedia.org/wiki/Builder) ou [FluentInterface](https://en.wikipedia.org/wiki/Fluent_interface)), toda chamada do método deve estar na mesma linha, separada por `.`

```java
Picasso.with(context).load("http://ribot.co.uk/images/sexyjoe.jpg").into(imageView);
```

```java
Picasso.with(context)
       .load("http://ribot.co.uk/images/sexyjoe.jpg")
       .into(imageView);
```
> De preferência com todos os `.` alinhados.

__Parâmetros longos__

Quando os métodos possuem vários parâmetros ou os mesmos são muito longos, você deve quebrar as linhas ao usar `,`:

```java
loadPicture(context, "http://ribot.co.uk/images/sexyjoe.jpg", imageViewProfilePicture, clickListener, "Title of the picture");
```

```java
loadPicture(context,
            "http://ribot.co.uk/images/sexyjoe.jpg",
            imageViewProfilePicture,
            clickListener,
            "Title of the picture");
```
> De preferência alinha os parâmetros também.

## 2.3 XML

### 2.3.1 Feche as próprias tags

Quando o elemento do XML não possui nenhum componente interno, você __deve__ fecha-lo.

Forma correta:

```xml
<TextView
	android:id="@+id/profileTextView"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" />
```

Forma __errada__ :

```xml
<!-- Sério, isso é muito feio -->
<TextView
    android:id="@+id/profileTextView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
```


### 2.3.2 Nomeando elementos de Layout

Os IDs dos elementos devem ser escritos utilizando __camelCase__.

#### 2.3.2.1 Nomeando...

Os IDs devem conter como sufixo o nome do componente.
Por exemplo:


| Elemento             | Sufixo              |
| -------------------  | ------------------- |
| `TextView`           | `TextView`          |
| `ImageView`          | `ImageView`         |
| `Button`             | `Button`            |
| `Menu`               | ---                 |

Exemplo de ImageView:

```xml
<ImageView
    android:id="@+id/profileImageView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```
> Utilizar o nome do componente como sufixo ou prefixo, é extremamente questionável.
Porém, utilizando o tipo do componente no ID nos trás problemas no futuro.
Ex. O seu projeto cresceu! Quando você quiser mapear um botão, estando todos os botões do projeto com `button_`,
você terá uma lista com 68468 IDs que iniciam com `button_`.
Levando isso em conta, adotamos esse modelo.

Exemplo de menu:

```xml
<menu>
	<item
        android:id="@+id/done"
        android:title="Done" />
</menu>
```

#### 2.3.2.2 Strings

Nomes de Strings começam com um prefixo que identifica a seção a que pertencem. Por exemplo `registration_email_hint` ou `registration_name_hint`. Se uma string não pertence a qualquer seção, então você deve seguir as seguintes regras:


| Prefixo              | Descrição                             |
| -------------------  | --------------------------------------|
| `error_`             | Erro da mensagem                      |
| `msg_`               | Mensagem normal                       |
| `title_`             | Título de um dialog, por exemplo      |
| `action_`            | Ação "Salvar", "Voltar", por exemplo  |



#### 2.3.2.3 Styles e Themes

A menos que o restante dos `resources`, os nomes de `styles` são escritos em __UpperCamelCase__.

### 2.3.3 Ordenando atributos

Como regra geral, você deve tentar grupo atributos semelhantes juntos. Uma boa maneira de ordenar os atributos mais comuns é:

1. View Id
2. Style
3. Layout width e layout height
4. Outros atributos do layout, ordenados por ordem alfabética
5. Renomeando os atributos, ordenados por ordem alfabética

## 2.4 Tests

### 2.4.1 Testes unitários

As classes de teste deve corresponder ao nome da classe dos testes que são alvo, seguido de `Test`. Por exemplo, se criar uma classe de teste que contém os testes para o `DatabaseHelper`, devemos chamar de `DatabaseHelperTest`.


Os métodos de teste são anotados com `@Test` e geralmente devem começar com o nome do método que está a ser testado, seguido de uma pré-condição e / ou o comportamento esperado.

* Template: `@Test void methodNamePreconditionExpectedBehaviour()`
* Exemplo : `@Test void signInWithEmptyEmailFails()`

Condição prévias e/ou comportamentos esperados nem sempre pode ser necessária se o teste é suficientemente clara sem eles.

As vezes, uma classe pode conter uma grande quantidade de métodos, que, ao mesmo tempo requerem vários testes para cada método. Neste caso, é recomendável dividir a classe de teste em várias classes de testes. Por exemplo, se o `DataManager` contém uma grande quantidade de métodos a gente pode querer dividi-lo em `DataManagerSignInTest`, `DataManagerLoadUsersTest`, etc..
Geralmente você vai ser capaz de ver o que os testes que pertencem um ao outro, porque eles têm dispositivos de [teste comuns](https://en.wikipedia.org/wiki/Test_fixture).

### 2.4.2 Usando Espresso

Cada classe de teste [Espresso](https://developer.android.com/training/testing/ui-testing/espresso-testing.html) normalmente tem como alvo uma `Activity`, portanto, o nome deve corresponder ao nome da `Activity` alvo seguido de teste, por exemplo, `SignInActivityTest`.

Ao usar a API do `Espresso` é uma prática comum colocar métodos encadeados em novas linhas.

```java
onView(withId(R.id.view))
        .perform(scrollTo())
        .check(matches(isDisplayed()))
```
---
