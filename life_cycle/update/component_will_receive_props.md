# Güncelleme ve `componentWillReceiveProps()` metodu

Bileşen için güncelleme evresinin nasıl başlayacağını bir önceki bölümde inceledik. Güncelleme evresinin ilk metodu `componentWillReceiveProps()` metodudur. Bu metot, bir bileşene yeni `props` değeri aktarıldığında çalıştırılır. `props` değeri aktarılması ne anlama gelir, inceleyelim.
 
 ## Bileşen `props` aktarılması

Örnek olarak bir Form bileşenimiz bir de Kisi isimli bileşenimiz olduğunu varsayalım. Form bileşeni bir `<input />` html elementi ile kullanıcıdan veri alarak ekranda görülen `isim` değerini değiştirebilmektedir. Bu `input` elementi bir `onChange` eventi ile Form'un `state` değerini güncellemektedir. Bu `state` değeri daha sonra Kisi bikeşenin bir `prop` olarak aktarılmaktadır.   
  
***Form.js***
```javascript
import React from 'react';
import Kisi from './Kisi';

export default class Form extends React.Component {
  constructor(props) {
    super(props);
    this.state        = { isim: '' } ;
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(event) {
    this.setState({ isim: event.currentTarget.value });
  }

  render() {
    return (
      <div>
        <input type="text" onChange={ this.handleChange } />
        <Kisi name={ this.state.isim } />
      </div>
    );
  }
}
```

Kullanıcı ne zaman `<input />` içerisine yazı girdiğinde bu işlem `Kisi` bileşeni için bir güncelleme başlatır. Kisi bileşeni üzerinde çalıştırılacak ilk metot `componentWillReceiveProps(nextProps)` olacaktır. Bu metot ile birlikte yeni isim değeri bir `prop` olarak bileşene aktarılmaktadır. Bu sayede yeni aktarılan `prop` değeri ile mevcut `prop` değerini karşılaştırıp, sonuca göre bazı aksiyonlar alabiliriz. Metot içerisinde bileşenin mevcut `props` değerine `this.props` ile ulaşabilirken, yeni aktarılmakta olan `props` değeri de metoda argüman olarak gönderilen `nextProps` değeridir. 
 
### State'in güncellenmesi

Peki neden `componentWillReceiveProps` metoduna ihtiyacımız var? Bu metot bize yeni bir `props` değeri aktarıldığını ve bileşenin bir güncelleme sürecine girebileceğini bilebilme imkanı verir. Burada ayrıca yeni `props` değerini okuyarak bileşenin mevcut `state` değerini de güncelleyebiliriz. Eğer birkaç `props` değeri üzerinden hesaplanan bir `state` değerimiz var ise, bu `state`'i burada yeni gelen `props` değeri üzerinden tekrar hesaplayarak `setState` metodu ile bileşenin 'state'ini güvenli bir şekilde güncelleyebiliriz.  

> Bu metot içerisinde çalıştırılacak bir `setState` çağrısı yeni bir `render()` tetiklemeyecektir. Bileşen içi bir `geçiş` (transition) durumunu yönetmek için bu metodu kullanabiliriz. Render metodu çalışmadan önce, yeni `props` değerlerini `state` içerisinde saklayıp, eski değerlere de `this.props` ile ulaşabiliriz. Eski ve yeni değerleri bu sayede `render` metodu içerisinde elde edip buna göre aksiyon alabiliriz.

### `Props` değşmemiş olasa da!

`componentWillReceiveProps` metodunun çalışmış olması, bileşene aktarılan `props`un değiştiği anlamına gelmez. Yani aynı bileşen aynı `props` değeri ile tekrar render edilse dahi, kendisine bir `props` değeri aktarıldığı için bu metot yine çalışacaktır. `props` değerinin değişip değişmediğini kontrol etmek bileşenin kendisine kalmıştır. Ayrıca React'in, verilen `props` değerlerinin değişip değişmediğini bilmesine imkan yoktur. Örneğe göz atarsak,

```jsx
var mydata = {bar: 'içecek'};
ReactDOM.render(<Bilesen data={mydata} />, container);

mydata.bar = 'yemek'
ReactDOM.render(<Bilesen data={mydata} />, container);

mydata.bar = 'tatlı'
ReactDOM.render(<Bilesen data={mydata} />, container);
```

burada `mydata` isimli bir nesne `prop` olarak bileşene aktarılarak, bileşen DOM üzerine render ediliyor. Örnekte toplam 2 kere `componentWillReceiveProps` metodu çağrılacaktır çünkü ilk render'da bu metot çalışmaz. Daha sonra `props` olarak verilen js objesi üzerinde değişiklikler yapılıyor ve bileşen tekrar render ediliyor. 

- Burada `data` isimli `prop` değerinin değişip değişmediğini `===` operatörü ile anlamak mümkün değil çünkü nesne aynı nesne ve hala aynı hafıza adresine işaret ediyor. Sadece iç değeri değişti. İç değer değişimi de ancak eski datanın derin bir kopyasını (`deep copy`) saklayarak ve yeni datanın iç değerleriyle tek tek karşılatırarak (`deep comparison`) mümkün olabilir ki bu da büyük nesnelerin karşılaştırılmasında performans problemlerine yol açacaktır.

- mydata nesnesi closure'lar içerisinde, bazı değişkenleri hapsetmiş fonksiyonlara referanslar içeriyor olabilir. React'in bu closure'lara gözatması ve değişip değişmediklerini anlamasının bir yolu yoktur.

- mydata nesnesi bir üst nesnenin render metodunda tekrar oluşturulan nesnelere referanslar içeriyor olabilir. Bu durumda nesnenin key-value değerleri değişmemiş olsa da, nesne baştan oluşturulduğu için farklı bir hafıza adresine işaret edecek ve kesinlikle değişmil olarak algılanacaktır. Gerçekten değişip değişmediğini anlamak için yine çok maliyetli bir işlem olan `deep comparison` yapmak gereklidir. Ayrıca bu nesne fonksiyonlar da içeriyor olabilir ki, iki fonksiyonu karşılaştırarak aynı olup olmadıklarını anlamanın güvenilir bir yolu yoktur.


> Örnek için makaleye gözatabilirsiniz [(A =&gt; B) !=&gt; (B =&gt; A)](https://facebook.github.io/react/blog/2016/01/08/A-implies-B-does-not-imply-B-implies-A.html) 

Buradaki temel sorun javascript'te `mutable` veriyapılarını nasıl davrandığı ile alakalıdır. Bu sefer de bir Array yapısını inceleyecek olur isel, 

```javascript
// temsili kod
this.setState({ data: [1, 2, 3] });

<Bilesen data={ this.state.data } />
```

Uygulamanın herhangibir yerinde data isimli array'i `push()` ile güncellediğimizde bu işlem Array'in içeriği değiştirir ancak kendisi değiştirmez. data array'ı yine aynı array örneği (instance) olarak kalacaktır. Aynı array örneği olarak kaldığı için de, içeriğinin değişip değişmediğini anlamak kolay bir işlem olmayacaktır. Çok ciddi performans sorunlarına yol açacak `deep comparison` gibi bir işlemden kaçınmak ve daha da büyük problemleri önlemek için, React her halükarda `componentWillReceiveProps` metodunu çalıştıracak ve `props` değerinin değişip değişmediğinin kararını bileşe bırakacaktır. 


### Ne zaman çalışmaz?
`componentWillReceiveProps()` metodu `state` değişimlerinde çalıştırılmaz.
 
 ```javascript
 // ...
 handleChange(event) {
    this.setState({ isim: event.currentTarget.value });
  }

  render() {
    return (
      <div>
        <input type="text" onChange={ this.handleChange } />
        <Kisi isim={ this.state.isim } />
      </div>
    );
  }
// ...
 ```

Kullanıcı `<input />` alanına metin girdiğinde `setState()` metodu çalışacaktır. Bu da, Form bileşeni üzerinde bir güncelleme evresi başlatacaktır. Form bileşeni için yeni `props` değeri aktarılmadığı için `componentWillReceiveProps` metodu da çalıştırılmayacaktır.

***Gelecek Bölüm:*** [`shouldComponentUpdate()` metodu kullanımı](using_should_component_update.md)
