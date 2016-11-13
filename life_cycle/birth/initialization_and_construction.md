# İlk yapılandırma
Bir bileşenin React öğesinden (ReactElement) türetilmesi sırasında, props ve state değerleri tanımlanır. Bu değerlerin nasıl tanımlandığı ES5 (`React.createClass()`) veya ES6 (`extend React.Component`) kullanımına bağlı olarak farklılık gösterir. Öncelikle props değerini inceleyelim.

## Props başlangıç değerleri
Daha önce bahsettiğimiz gibi, React öğesi Bileşene aktarmak üzere props değerlerini barındırır. Çoğu zaman bileşen için tüm props değerlerinin belirtilmesi gerekmez. Bu durumda, eğer props değeri belirtilmemiş ise, bizim props başlangıç değeri olarka tanımladığımız değer kullanılır. 

Örneğin, isim ve yaş gösteren basit bir bileşen tanımlayalım. 

```javascript
import React from 'react';

export default class Kisi extends React.Component {
  render() {
    return (
      <div>{ this.props.isim } (age: { this.props.yas })</div>
    );
  }
}
```

Bu durumda, Kisi adlı bileşene `isim` ve `yas` adında iki props değeri vermemiz gerekmektedir. Eğer yaş değerini zorunlu olmaktan çıkarmak istiyor isek, bir başlangıç değeri atamamız gerekir. 

**ES6 için**
```javascript
import React from 'react';

class Kisi extends React.Component {
  render() {
    return (
      <div>{ this.props.isim } (age: { this.props.yas })</div>
    );
  }
}

Kisi.defaultProps = { yas: 'Bilinmiyor' };

export default Kisi;
```

**ES5 ve öncesi**

```javascript
var Kisi = React.createClass({
  getDefaultProps: function() {
    return ({ yas: 'Bilinmiyor' });
  },
  
  render: function() {
    return (
      <div>{ this.props.isim } (age: { this.props.yas })</div>
    );
  }
});
```

İki kullanımın da sonucu aynıdırı. Eğer yaş prop'unu vermeden bileşeni render edecek olursa, yaş kısmında `Bilinmiyor` yazdığını görürüz. 

React, props değeri için bağlangıç değerleri ile bileşene aktarılan değerleri üst üste birleştirerek (merge) kullanır. Yani yaş değeri bileşene verilir ise, başlangıç değeri üzerine yazılır ve yas prop'unun değeri `Bilinmiyor` değerinden verilen değere dönüşür. React bu işlemi [`Object.assign()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) metodu ile yapmaktadır. 

```javascript
// React Kütüphanesi kodu
if (Constructor.getDefaultProps) {
   Constructor.defaultProps = Constructor.getDefaultProps();
}

// bileşen props değerleri merge ediliyor
this.props = Object.assign(Constructor.defaultProps, elementInstance.props);
```

React öncelikle verilen bileşen örneğini (class instance), `getDefaultProps()` metodunu implemente edip etmediğine bakar. Eğer edilmiş ise bu metodu kullanarak başlangıç props değerlerini atar. Eğer ES6 sınıflarını kullanıyor isek, props değeri direk sınıf üzerinden tanımlanır. (`bilesen.defaultProps =  {...}`). Ve daha sonra bileşene verilen her props değeri, başlangıç props değeri üzerine yazılarak güncellenir. Böylece eksik verilen props değerleri yerine başlangıç props değerleri kullanılırken, bileşene verilen değerler güncel olarak tutulur.

### Props değeri için `null` ve `undefined`

Bileşenin ilk props değerleri atarnırken, React bileşene aktarılan `null` değerlerini de merge eder. Çünkü `null` değeri olmasa da, tanımlanmış bir değerdir. 

```javascript
<Kisi isim="Ata" yas={ null } />
```

Bu durumda, başlangıç props değerlerinde tanımlı olan yas değeri (`Bilinmiyor` olarka tanımlamıştık) verilen `null` ile güncellenir. Ve bileşenin çıktısı `<div>Ata (yas:)</div>` şeklinde olur. Ancak burada `undefined` değeri verilir ise, doğal olarak React bu değeri verilmemiş sayar ve yas prop'u için başlangıç değeri olan `Bilinmiyor`'u kullanır. 

Bir bileşen için props değerleri aktarılırken akılda tutulması gereken bir durumdur. Beklenilenin dışında bir çıktı alınmasına sebep olur ve takip edilmesi zahmetli olabilen bir hatadır.


## Başlangıç state değeri

Başlangıç props değeri merge edilip son haline kavuştuktan sonra, bileşen için dahili veri deposu olan `state` atamasına geçilir. Bu süreç bileşen örneğinin çıkartılması (construction) zamanında yapılır. Props değerinin aksine `state` tamamen bileşen içerisinde, izole bir veri kümesi olup, bileşene dışarıdan aktarılmaz. 

Yine iki şekilde, ES6 Sınıfları ya da klasik js ile tanımalada küçük farklılıklar bulunmaktadır. 
 
 **ES6 Class**
```javascript
import React from 'react';

class Kisi extends React.Component {
  constructor(props) {
    super(props);
    this.state = { sayi: 0 };
  }

  render() {
    return (
      <div>{ this.props.isim } (age: { this.props.yas })</div>
    );
  }
}

Kisi.defaultProps = { age: 'Bilinmiyor' };

export default Kisi;
```

`React.createClass` metodu için `getInitialState()` isimli yardımcı bir metod çağrılır. React her bileşen örneği çıkardğında bu metodu çağırarak bileşenin başlangıç `state` değerini elde eder.

**ES6 öncesi**

```javascript
var Kisi = React.createClass({
  getDefaultProps: function() {
    return ({ age: 'Bilinmiyor' });
  },
  
  getInitialState: function() {
    return ({ sayi: 0 });
  },
  
  render: function() {
    return (
      <div>{ this.props.isim } (age: { this.props.yas })</div>
    );
  }
});
```

Eğer herhangi bir `state` değeri tanımlamamış ya da `getInitialState()` metodu ile bir değer döndürmemiş ise, `state` değeri `undefined` olacaktır. Başlangıç state değeri boş bir nesne yerine (`{}`) `undefned` olduğu için eğer herhangi bir `state` değerine ulaşılmaya çalışılırsa hata alınacaktır. Örneğin,

 
 ```javascript
 class Kisi extends React.Component {
  render() {
    // Burada bir hata alınacak
    console.log(this.state.dogumYeri);
    return (
      <div>{ this.props.isim } (age: { this.props.yas })</div>
    );
  }
}
 ```

Buradaki log metodu hata verecektir çünkü `this.state` değeri tanımlanmış değildir. Bu sorunu çözmek için ya başlangıç state değeri ataması yapılmalıdır.

Genel olarak, bileşen içerisinde kullanılan her bir `state` değeri için başlangıç değeri tanımlanması daha doğrudur. Bazı durumlarda bu başlangıç değerleri `null` veya `undefined` dahi olabilir. 

***Gelecek Bölüm:***[`componentWillMount()` metodu](premounting_with_componentwillmount.md)
