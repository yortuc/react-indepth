# Bileşen `render()` metodu

Bileşenimiz için yapılandırma aşamasını tamamladıktan sonra, şimdi ilk kez render etme aşamasıan geldik. JSX kullanarak React öğeleri oluşturup (ReactElement), bunları geri döndürüyoruz. Bu aşamada bileşenin ´state´ ve ´props´ değerlerine erişebiliyor ve zaten bileşenin nasıl render edilmesi gerektiğine bu değelere göre karar veriyoruz. `componentWillMount()` metodunda eğer `state` değeri değişikliğe uğramış ise bu aşamada güncellemeler tamamen uygulanmış olarak karşımıza çıkacaktır.

Diğer yaşan döngüsü metodlarının aksine, ´render´ metodu birden fazla yaşam döngüsü aşamasında karşımıza çıkar. Bileşenin doğum aşasında çalıştırıldığı gibi, gelişme aşamasında da birçok kez çalıştırılır.

Her durumda, temel kural ´render´ metodunun pür (pure) fonksiyon olarak kalması gerektiğidir. Yani `render` metodu bileşenin `state`'ine etki etmemeli, Native UI katmanı üzerinde bir değişiklik yapmamalı, yani kısaca genel uygulama `state`'ini mutate etmemelidir. Eğere `render` metodu içerisinde `setState` metoduna izin verilse ve bileşen `state` değeri değiştirilebilse idi, bu değişim de bir `render` tetikleyeceğinden sonsuz bir döngüye girilmiş olurdu. 

`render` metodu içerisinde `setState` çağrılması gibi durumlarda React bize uyarı verir. Örneğin,  

```jsx
render() {
  // bunu yapmayın!
  this.setState({ foo: 'bar' });
  return (
    <div className={ classNames('person', this.state.mode) }>
      { this.props.name } (age: { this.props.age })
    </div>
  );
}
```

Bu durumda React, browser konsolunda bir uyarı mesajı gösterecektir:

> Warning: setState(...): Cannot update during an existing state transition (such as within `render`). Render methods should be a pure function of props and state.

## Native UI access in `render()` is often fatal
React will also warn you if you try to access the Native UI elements in the render pass.

```javascript
render() {
  // BAD: Don't do this either!
  let node = ReactDOM.findDOMNode(this);
  return (
    <div className={ classNames('person', this.state.mode) }>
      { this.props.name } (age: { this.props.age })
    </div>
  );
}
```

> VM943:45 Warning: Person is accessing getDOMNode or findDOMNode inside its render(). render() should be a pure function of props and state. It should never access something that requires stale data from the previous render, such as refs. Move this logic to componentDidMount and componentDidUpdate instead.

In the above example, it may seem safe since you are just querying the node. But, as the warning states, we might be querying potentially old data. But in our case, during the Birth phase, this would be a fatal error.

> Uncaught Invariant Violation: findComponentRoot(..., .0): Unable to find element. This probably means the DOM was unexpectedly mutated (e.g., by the browser), usually due to forgetting a &lt;tbod&gt; when using tables, nesting tags like &lt;form&gt;, &lt;p&gt;, or &lt;a&gt;, or using non-SVG elements in an &lt;svg&gt; parent. Try inspecting the child nodes of the element with React ID `Person`.

This is one of those cases where the React error doesn't clearly point to the cause of the problem. In our case we didn't modify the DOM, so it feels like an unclear and potentially misleading error. This kind of error can cause React developers a lot of pain early on. Because we instinctually look for a place where we are changing the Native UI.

The reason we get this error is because during the first render pass the Native UI elements we are trying to access do not exist yet. We are essentially asking React to find a DOM node that doesn't exist. Generally, when `ReactDOM` can't find the node, this is because something or someone mutated the DOM. So, React falls back to the most common cause. 

As you can see, having an understanding of the Life Cycle can help troubleshoot and prevent these often un-intuitive issues.

***Up Next:***[ Managing Children Components and Mounting](managing_children_components_and_mounting.md)

---
[^1] These warnings come out of the development mode in React. You can also use the [React Development Tools](https://github.com/facebook/react-devtools) to help debug and explore React components.
