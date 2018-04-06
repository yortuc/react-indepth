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

Bu durumda React, browser konsolunda bir uyarı mesajı vererek, mevcut bir `state` değişimi esnasında `state`'in güncellenemeyceğini belirtir. Render metodu bileşen `props` ve `state` değerlerine bağlı bir pür fonksiyon olmalıdır.

> Warning: setState(...): Cannot update during an existing state transition (such as within `render`). Render methods should be a pure function of props and state.

## `render()` metodu içerisinde Native UI katmanına erişim
Eğer `render()` metodu içerisinden Native UI katmanına erişmeye çalışırsak, bu durumda da React bize bir uyarı mesajı verecektir.

```javascript
render() {
  // bunu da yapmayın!
  let node = ReactDOM.findDOMNode(this);
  return (
    <div className={ classNames('kisi', this.state.durum) }>
      { this.props.isim } (age: { this.props.yas })
    </div>
  );
}
```

Uyarı mesajı

> VM943:45 Warning: Person is accessing getDOMNode or findDOMNode inside its render(). render() should be a pure function of props and state. It should never access something that requires stale data from the previous render, such as refs. Move this logic to componentDidMount and componentDidUpdate instead.

Burada da yine `render()` metodunun `pür` olması gerektiği kuralını ihlal ediyoruz. `findDOMNode()` metodu ile DOM üzerinde, bir önceki render'de oluşturulmuş bir `bilgi`yi sorguluyor olabiliriz. Hatta ki ilk `render()` metodunda bu sorgulama yapılıyor ise, henüz bu bileşen için DOM üzerinde birşey oluşturulmadığı için sorgulama sonucu hata döndürecektir. 

Madem ki `render()` metodu pür bir fonksiyon olmalıdır, ihtiyacı olan tek bilgi bileşenin o andaki `props` ve `state` değerleridir. Bunlar dışında hiçbir `veri` render esnasında kullanılmamalıdır.

İkinci mesajda görüleceği üzere henüz ilgili DOM elemanı oluşturulmadığı için DOM node'u bulunamamıştır.


***Gelecek Bölüm:***[Çocuk bileşenlerin yönetimi ve Mounting](managing_children_components_and_mounting.md)

---
[^1] Bu uyarılar React, `development` (geliştirme) modunda çalıştırıldığında alınır. React bileşenlerinin iç durumlarını (`state` ve `props` değerleri) incelemek için çok yararlı [React geliştirme araçları](https://github.com/facebook/react-devtools) browser eklentisini kullanabilirsiniz. 
