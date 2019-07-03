# Строгий режим

`StrictMode` – инструмент для обнаружения потенциальных проблем в приложении. Также как и `Fragment`, `StrictMode` не рендерит видимого UI. Строгий режим активирует дополнительные проверки и предупреждения для своих потомков.

> Примечание:
>
> Проверки строгого режима работают только в режиме разработки; _они не оказывают никакого эффекта в продакшен-сборке_.

Строгий режим может быть включён для любой части приложения. Например:
`embed:strict-mode/enabling-strict-mode.js`

В примере выше проверки строгого режима _не_ будут выполняться для компонентов `Header` и `Footer`. Однако будут выполнены для `ComponentOne` и `ComponentTwo`, а также для всех их потомков.

На данный момент `StrictMode` помогает в:

- [Обнаружении небезопасных методов жизненного цикла](#identifying-unsafe-lifecycles)
- [Предупреждении об использовании устаревшего API строковых реф](#warning-about-legacy-string-ref-api-usage)
- [Предупреждении об использовании устаревшего метода findDOMNode](#warning-about-deprecated-finddomnode-usage)
- [Обнаружении неожиданных побочных эффектов](#detecting-unexpected-side-effects)
- [Обнаружении устаревшего API контекста](#detecting-legacy-context-api)

Дополнительные проверки будут включены в будущих релизах React.

## Обнаружение небезопасных методов жизненного цикла {#identifying-unsafe-lifecycles}

[В этой статье](https://ru.reactjs.org/blog/2018/03/27/update-on-async-rendering.html) рассматриваются причины, почему некоторые методы жизненного цикла небезопасно использовать в асинхронных React-приложениях. Если в приложении подключены сторонние библиотеки, то отследить использование таких методов довольно тяжело. К счастью, тут может помочь строгий режим!

Когда строгий режим включён, React составляет список всех классовых компонентов, которые используют небезопасные методы жизненного цикла, и отображает информацию о них таким образом:

![Жизненный цикл](strict-mode-unsafe-lifecycles-warning.png)

Если избавиться от проблем, выявленных в строгом режиме, _уже сегодня_, то это позволит получить все преимущества асинхронного рендеринга в будущих релизах React.

### Предупреждение об использовании устаревшего API строковых реф {#warning-about-legacy-string-ref-api-usage}

Ранее React предоставлял два способа управления рефами: устаревшие строковые рефы и колбэк API. Хотя строковые рефы и были более удобным способом, они имели [несколько недостатков](https://github.com/facebook/react/issues/1373). Поэтому мы рекомендовали [использовать колбэки вместо них](refs-and-the-dom.md#legacy-api-string-refs).

В React 16.3 добавлен третий способ, который предлагает удобство строковых рефов и лишён каких-либо недостатков:
`embed:16-3-release-blog-post/create-ref-example.js`

Поскольку объекты-рефы стали заменой строковых реф, строгий режим теперь предупреждает об использовании строковых реф.

> **Примечание:**
>
> Колбэк-рефы по-прежнему поддерживаются вместе с новым API-методом `createRef`.
>
> Вам не обязательно заменять колбэк-рефы в ваших компонентах. Их использование более гибкое, поэтому они считаются продвинутой возможностью.

[Ознакомьтесь с новым API-методом `createRef` здесь.](refs-and-the-dom.md)

### Предупреждение об использовании устаревшего метода findDOMNode {#warning-about-deprecated-finddomnode-usage}

Ранее React использовал `findDOMNode` для поиска DOM-узла в дереве по указанному экземпляру класса. В большинстве случаев этот метод не используется, поскольку можно [привязать реф непосредственно к DOM-узлу](refs-and-the-dom.md#creating-refs).

`findDOMNode` может использоваться для классовых компонентов, однако это нарушает уровни абстракции, позволяя родительскому компоненту требовать, чтобы происходил рендер определённого дочернего элемента. Это приводит к проблемам при рефакторинге, когда не удаётся изменить детали реализации компонента, так как родитель может использовать DOM-узел этого компонента. `findDOMNode` возвращает только первый дочерний элемент, но с использованием фрагментов компонент может рендерить несколько DOM-узлов. `findDOMNode` выполняет поиск только один раз. Затем метод возвращает ранее полученный результат при вызове. Если дочерний компонент рендерит другой узел, то это изменение никак не отследить. Поэтому `findDOMNode` работает, только когда компоненты возвращают единственный и неизменяемый DOM-узел.

Вместо этого, можно передать реф в компонент и передать его далее в DOM используя [перенаправление рефов](forwarding-refs.md#forwarding-refs-to-dom-components).

Также можно добавить компоненту DOM-узел как обёртку и прикрепить реф непосредственно к этой обёртке.

```javascript{4,7}
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    this.wrapper = React.createRef()
  }
  render() {
    return <div ref={this.wrapper}>{this.props.children}</div>
  }
}
```

> Примечание:
>
> CSS-выражение [`display: contents`](https://developer.mozilla.org/ru/docs/Web/CSS/display#display_contents) может применяться для исключения узла из макета (layout).

### Обнаружение неожиданных побочных эффектов {#detecting-unexpected-side-effects}

React работает в два этапа:

- **Этап рендеринга (render phase)** определяет, какие изменения необходимо произвести, например, в DOM. В течение этого этапа React вызывает `render`, а затем сравнивает полученный результат с результатом предыдущего рендера.
- **Этап фиксации (commit phase)** – в нём React применяет любые изменения. В случае React DOM – это этап, когда React вставляет, обновляет и удаляет DOM-узлы. В течение этого этапа React вызывает методы жизненного цикла `componentDidMount` и `componentDidUpdate`.

Этап фиксации обычно не занимает много времени, что нельзя сказать про этап рендеринга. По этой причине, готовящийся асинхронный режим (который по умолчанию ещё не включён) делит работу на части, периодически останавливает и возобновляет работу, чтобы избежать блокировки браузера. Это означает, что на этапе рендеринга React может вызвать методы жизненного цикла более чем один раз перед фиксацией, либо вызвать их без фиксации (из-за возникновения ошибки или прерывания с большим приоритетом).

Этап рендеринга включает в себя следующие методы жизненного цикла:

- `constructor`
- `componentWillMount`
- `componentWillReceiveProps`
- `componentWillUpdate`
- `getDerivedStateFromProps`
- `shouldComponentUpdate`
- `render`
- Функции обновления `setState` (первый аргумент)

Поскольку вышеупомянутые методы могут быть вызваны более одного раза, важно, чтобы они не приводили к каким-либо побочным эффектам. Игнорирование этого правила может привести к множеству проблем, включая утечки памяти и недопустимое состояние приложения. К сожалению, такие проблемы тяжело обнаружить из-за их [недетерминированности](https://ru.wikipedia.org/wiki/%D0%94%D0%B5%D1%82%D0%B5%D1%80%D0%BC%D0%B8%D0%BD%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%BD%D1%8B%D0%B9_%D0%B0%D0%BB%D0%B3%D0%BE%D1%80%D0%B8%D1%82%D0%BC).

Строгий режим не способен автоматически обнаруживать побочные эффекты, но помогает их отследить, сделав более детерминированными. Такое поведение достигается путём двойного вызова следующих методов:

- Метод `constructor` классового компонента
- Метод `render`
- Функции обновления`setState` (первый аргумент)
- Статически метод `getDerivedStateFromProps`

> Примечание:
>
> Это применимо только в режиме разработки. _Методы жизненного цикла не вызываются дважды в продакшен-режиме._

Рассмотрим следующий пример:
`embed:strict-mode/side-effects-in-constructor.js`

На первый взгляд данный пример не кажется проблемным. Но если метод `SharedApplicationState.recordEvent` не является [идемпотентным](https://ru.wikipedia.org/wiki/%D0%98%D0%B4%D0%B5%D0%BC%D0%BF%D0%BE%D1%82%D0%B5%D0%BD%D1%82%D0%BD%D0%BE%D1%81%D1%82%D1%8C#%D0%92_%D0%B8%D0%BD%D1%84%D0%BE%D1%80%D0%BC%D0%B0%D1%82%D0%B8%D0%BA%D0%B5), тогда создание этого компонента несколько раз может привести к недопустимому состоянию приложения. Такие труднонаходимые ошибки могут никак не проявить себя во время разработки или быть настолько редкими, что останутся незамеченными.

Двойной вызов таких методов, как конструктор компонента, позволяет строгому режиму легко обнаружить подобные проблемы.

### Обнаружение устаревшего API контекста {#detecting-legacy-context-api}

Использование устаревшего API контекста очень часто приводило к ошибкам и поэтому он будет удалён в будущей мажорной версии. Пока что этот API доступен во всех релизах 16.x, но в строгом режиме будет выведено следующее предупреждение:

![Обнаружение устаревшего API контекста](warn-legacy-context-in-strict-mode.png)

Ознакомьтесь с [документацией нового API контекста](context.md), чтобы упростить переход на новую версию.