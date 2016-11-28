# Yüksek derece bileşenler

Bileşen kompozisyon yöntemlerinden son olarak *Yüksek derece bileşenler*i inceleyeceğiz. [Dan Abramov'un](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750#.b74nxbqew) belirttiği gibi, yükske dereceli bileşenler ilk olarak [Sebastian Markbåge](https://gist.github.com/sebmarkbage/ef0bf1f338a7182b6775) tarafından bir gist'te önerilmiştir. Temel fikir, bir fonksiyon oluşturup, bu fonksiyona çeşitli bileşenleri parametre olarak göndermektir. Bu fonksiyon da, kendisine verilen bileşenleri kapsayan yeni bir bileşen döndürmektedir. 

React ES6 kullanımı ile birlikte mixin desteğini kaldırdığında, bu tarz bir kullanıma ihitiyaç duyulmaya başlandı. Genel olarak mixin'ler, bir bileşene çeşitli özellikler kazandıran tekrar kullanılabilir kod parçalarıydı. ES6'daki javascript class'ları ile birlikte kaldırılan mixin'lerin yerini tutacak bir mekanizmaya ihtiyaç duyuldu.


### Form grubu örneği

Verilen bir bileşeni form grubu tagleri içerisine saran ve isteğe bağlı bir `<label>` alanı içeren yüksek dereceli bir bileşen örneğini inceleyelim. Bileşenin amacı `<label>` barındıran ya da barındırmayan iki farklı çıktı alabilmek.

  
```html
  <!-- label -->
  <div class="form-group">
    <label class="form-label" for="isim">İsim:</label>
    <input type="text" name="isim" />
  </div>

  <!-- label olmadan -->
  <div class="form-group">
    <input type="text" name="soyAd" />
  </div>
```

Sürekli karşılaşılabilecek bir durum olduğu için, label kullanılıp kullanılmamasına göre gerekli bileşenlerin oluşturulma işini yüksek derece bileşene devredebiliriz.

 
 **formGroup.js**
```javascript
import React from 'react';
import { isString } from 'lodash';

function formGroup(Component, config) {
  const FormGroup = React.createClass({
    __renderLabel() {
      // Lodash#isString metodu ile verilen parametrenin strin olup olmadığını kontrol et
      if (isString(this.props.label)) {
        return(
          <label className="form-label" htmlFor={ this.props.name }>
            { this.props.label }
          </label>
        );
      }
    },

    __renderElement() {
      // parametre olarak bir element mi yoksa bileşen mi verildi?
      // (bileşen Profil, element: <input type="text" /> )
      if (React.isValidElement(Component)) return React.cloneElement(Component, this.props);
      return( <Component { ...this.props } />);
    },

    render() {
      return(
        <div className="form-group">
          { this.__renderLabel() }
          { this.__renderElement() }
        </div>
      );
    }
  });

  return(<FormGroup { ...config } />);
}

export default formGroup;
```

Yüksek derece bileşeni kullanmak için:

**index.js**
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import formGroup from './higherOrderComponents/formGroup';

let MyComponent = React.createClass({
  render() {
    return (
      <div>
        { formGroup(<input type="text" />, { label: 'İsim:', name: 'txtIsim' }) }
      </div>
    );
  }
});

ReactDOM.render(<MyComponent />, document.getElementById('mount-point'));
```

Yüksek dereceli bileşen için `Component` ve `config` şeklinde iki parametre alan `formGroup` adında bir fonksiyon oluşturuyoruz. 


```javascript
function formGroup(Component, config) {
  ...
}

export default formGroup;
```

From grubu içerisinde sarmak istediğimiz bileşeni `Component` parametresi olarak gönderiyoruz. Fonksiyon içerisinde bir React bileşeni oluşturup, `config` parametresi `props` olacak şekilde bir element örneği oluşturup geri döndürüyoruz.

```javascript
const FormGroup = React.createClass({
  ...
});

return(<FormGroup { ...config } />);
```

Burada [ES6 spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) yardımı ile `config` parametresi ile gelen javasript nesnesini elemente `props` olarak aktarıyoruz. Render metodunda bir `<div>` elementi ile form grubun oluşturarak, gerekli ise label ile birlikte render ediyoruz.

```javascript
render() {
  return(
    <div className="form-group">
      { this.__renderLabel() }
      { this.__renderElement() }
    </div>
  );
}
```

`__renderLabel()` metodunda[^1] verilen parametrenin bir `string` olup olmadığını anlamak için [Lodash `isString`](https://lodash.com/docs#isString) metodunu kullandık. Eğere `string` ise, label'i render ediyoruz. Eğer değilse, biz bir değer döndürmediğimiz için fonksiyon `undefined` döndürcek ve label render edilmeyecektir.

```javascript
__renderLabel() {
  // check if the passed value is a string using Lodash#isString
  if (isString(this.props.label)) {
    return(
      <label className="form-label" htmlFor={ this.props.name }>
        { this.props.label }
      </label>
    );
  }
},
```

Son olarak, `Component` parametresini kontrol ediyoruz. Fonksiyonun hem element hem de bileşen ile çalışabilmesini istiyoruz. 

```javascript
formGroup(<input type="text" />, { label: 'İsim:', name: 'txtIsim' })
```
Yukarıdaki örnekte bir input elementi verdiğimiz için fonksiyon bu elementi form grubu içerisine gömecektir. Bileşen de verebiliriz:

```javascript
formGroup(Profil, { label: 'İsim:', name: 'txtIsim' })
```

İki durumu da desteklemek için `__renderElement()` içerisinde verilen parametreyi kontrol ediyor ve ona uygun aksiyonu alıyoruz.


```javascript
__renderElement() {
  // parametre olarak bir element mi yoksa bileşen mi verildi?
  // (bileşen Profil, element: <input type="text" /> )
  if (React.isValidElement(Component)) return React.cloneElement(Component, this.props);
  return( <Component { ...this.props } />);
},
```

Eğere `Component` parametresi bir element ise, [React.cloneElement](https://facebook.github.io/react/docs/top-level-api.html#react.cloneelement)  metodu ile elementin kopyasını oluşturarak, `props` değerini aktarıyoruz. Eğer parametre bileşen ise zaten bileşen örneğini oluşturup geri döndürüyoruz.

Yüksek derece bileşenler ile elimizdeki veriye bakarak daha karmaşık kararlar verebilir ve elde edilmesi gereken çıktıya ulaşabiliriz. Daha ileri örnekler için Dan Abramov'un bu konudaki:

-*[Mixins Are Dead. Long Live Composition](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750#.9y0gg1ix5)* 

ve @franlplant'ın 

-*[React Higher Order Components in depth](https://medium.com/@franleplant/react-higher-order-components-in-depth-cf9032ee6c3e#.d38rbnsu8)* 

makalelerini inceleyebilirsiniz.

---

[^1] Bu örnekte kısmi render metodlarının başına `__` ekleyerek bunların tamamen bileşen ile alakalı dahili metotlar olduğunu belirtiyoruz. Bu tamamen isteğe bağlı olan bir yaklaşımdır.
