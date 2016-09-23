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

### 2.2.14 Arguments in Fragments and Activities

When data is passed into an `Activity `or `Fragment` via an `Intent` or a `Bundle`, the keys for the different values __must__ follow the rules described in the section above.

When an `Activity` or `Fragment` expects arguments, it should provide a `public static` method that facilitates the creation of the relevant `Intent` or `Fragment`.

In the case of Activities the method is usually called `getStartIntent()`:

```java
public static Intent getStartIntent(Context context, User user) {
	Intent intent = new Intent(context, ThisActivity.class);
	intent.putParcelableExtra(EXTRA_USER, user);
	return intent;
}
```

For Fragments it is named `newInstance()` and handles the creation of the Fragment with the right arguments:

```java
public static UserFragment newInstance(User user) {
	UserFragment fragment = new UserFragment;
	Bundle args = new Bundle();
	args.putParcelable(ARGUMENT_USER, user);
	fragment.setArguments(args)
	return fragment;
}
```

__Note 1__: These methods should go at the top of the class before `onCreate()`.

__Note 2__: If we provide the methods described above, the keys for extras and arguments should be `private` because there is not need for them to be exposed outside the class.

### 2.2.15 Line length limit

Code lines should not exceed __100 characters__. If the line is longer than this limit there are usually two options to reduce its length:

* Extract a local variable or method (preferable).
* Apply line-wrapping to divide a single line into multiple ones.

There are two __exceptions__ where it is possible to have lines longer than 100:

* Lines that are not possible to split, e.g. long URLs in comments.
* `package` and `import` statements.

#### 2.2.15.1 Line-wrapping strategies

There isn't an exact formula that explains how to line-wrap and quite often different solutions are valid. However there are a few rules that can be applied to common cases.

__Break at operators__

When the line is broken at an operator, the break comes __before__ the operator. For example:

```java
int longName = anotherVeryLongVariable + anEvenLongerOne - thisRidiculousLongOne
        + theFinalOne;
```

__Assignment Operator Exception__

An exception to the `break at operators` rule is the assignment operator `=`, where the line break should happen __after__ the operator.

```java
int longName =
        anotherVeryLongVariable + anEvenLongerOne - thisRidiculousLongOne + theFinalOne;
```

__Method chain case__

When multiple methods are chained in the same line - for example when using Builders - every call to a method should go in its own line, breaking the line before the `.`

```java
Picasso.with(context).load("http://ribot.co.uk/images/sexyjoe.jpg").into(imageView);
```

```java
Picasso.with(context)
        .load("http://ribot.co.uk/images/sexyjoe.jpg")
        .into(imageView);
```

__Long parameters case__

When a method has many parameters or its parameters are very long, we should break the line after every comma `,`

```java
loadPicture(context, "http://ribot.co.uk/images/sexyjoe.jpg", mImageViewProfilePicture, clickListener, "Title of the picture");
```

```java
loadPicture(context,
        "http://ribot.co.uk/images/sexyjoe.jpg",
        mImageViewProfilePicture,
        clickListener,
        "Title of the picture");
```

### 2.2.16 RxJava chains styling

Rx chains of operators require line-wrapping. Every operator must go in a new line and the line should be broken before the `.`

```java
public Observable<Location> syncLocations() {
    return mDatabaseHelper.getAllLocations()
            .concatMap(new Func1<Location, Observable<? extends Location>>() {
                @Override
                 public Observable<? extends Location> call(Location location) {
                     return mRetrofitService.getLocation(location.id);
                 }
            })
            .retry(new Func2<Integer, Throwable, Boolean>() {
                 @Override
                 public Boolean call(Integer numRetries, Throwable throwable) {
                     return throwable instanceof RetrofitError;
                 }
            });
}
```

## 2.3 XML style rules

### 2.3.1 Use self closing tags

When an XML element doesn't have any contents, you __must__ use self closing tags.

This is good:

```xml
<TextView
	android:id="@+id/text_view_profile"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" />
```

This is __bad__ :

```xml
<!-- Don\'t do this! -->
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
```


### 2.3.2 Resources naming

Resource IDs and names are written in __lowercase_underscore__.

#### 2.3.2.1 ID naming

IDs should be prefixed with the name of the element in lowercase underscore. For example:


| Element            | Prefix            |
| -----------------  | ----------------- |
| `TextView`           | `text_`             |
| `ImageView`          | `image_`            |
| `Button`             | `button_`           |
| `Menu`               | `menu_`             |

Image view example:

```xml
<ImageView
    android:id="@+id/image_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```

Menu example:

```xml
<menu>
	<item
        android:id="@+id/menu_done"
        android:title="Done" />
</menu>
```

#### 2.3.2.2 Strings

String names start with a prefix that identifies the section they belong to. For example `registration_email_hint` or `registration_name_hint`. If a string __doesn't belong__ to any section, then you should follow the rules below:


| Prefix             | Description                           |
| -----------------  | --------------------------------------|
| `error_`             | An error message                      |
| `msg_`               | A regular information message         |
| `title_`             | A title, i.e. a dialog title          |
| `action_`            | An action such as "Save" or "Create"  |



#### 2.3.2.3 Styles and Themes

Unless the rest of resources, style names are written in __UpperCamelCase__.

### 2.3.3 Attributes ordering

As a general rule you should try to group similar attributes together. A good way of ordering the most common attributes is:

1. View Id
2. Style
3. Layout width and layout height
4. Other layout attributes, sorted alphabetically
5. Remaining attributes, sorted alphabetically

## 2.4 Tests style rules

### 2.4.1 Unit tests

Test classes should match the name of the class the tests are targeting, followed by `Test`. For example, if we create a test class that contains tests for the `DatabaseHelper`, we should name it `DatabaseHelperTest`.

Test methods are annotated with `@Test` and should generally start with the name of the method that is being tested, followed by a precondition and/or expected behaviour.

* Template: `@Test void methodNamePreconditionExpectedBehaviour()`
* Example: `@Test void signInWithEmptyEmailFails()`

Precondition and/or expected behaviour may not always be required if the test is clear enough without them.

Sometimes a class may contain a large amount of methods, that at the same time require several tests for each method. In this case, it's recommendable to split up the test class into multiple ones. For example, if the `DataManager` contains a lot of methods we may want to divide it into `DataManagerSignInTest`, `DataManagerLoadUsersTest`, etc. Generally you will be able to see what tests belong together because they have common [test fixtures](https://en.wikipedia.org/wiki/Test_fixture).

### 2.4.2 Espresso tests

Every Espresso test class usually targets an Activity, therefore the name should match the name of the targeted Activity followed by `Test`, e.g. `SignInActivityTest`

When using the Espresso API it is a common practice to place chained methods in new lines.

```java
onView(withId(R.id.view))
        .perform(scrollTo())
        .check(matches(isDisplayed()))
```

# License

```
Copyright 2015 Ribot Ltd.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
