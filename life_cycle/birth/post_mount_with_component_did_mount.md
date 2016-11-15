# `componentDidMount()` metodu ve Mount sonrası

Bileşenin doğum yaşam döngüsü sürecinin son aşaması `componentDidMount()` metodudur. Bu metod, bileşen ve sahip olduğu tüm çocuk (alt) bileşenler render edilip Native UI katmanına mount edildikten sonra sadece tek bir kez çalıştırılır. Metod içerisinde artık Native UI katmanındaki elemanlara (DOM üzerine çalışılıyor ise html elementlerine) erişebilir, çocuk elementleri `ref` tanımlamaları ile sorgulayabiliriz. 

 The last step in the Birth/Mount life cycle phase is our post-mount access via `componentDidMount()`. 

## Çalışma sırası

`componentWillMount()` metodunda olduğu gibi, `componentDidMount()` metodu da bileşenin yaşam döngüsü boyunca sadece tek bir kez çalıştırılır. En üst bileşenden başlanarak alt bileşenlere doğru sırayla çağrılan diğer doğum metodlarının aksine (örneğin render metodu öncelikle üst bikeşen için daha sonra sıra ile alt bileşenler için çalıştırılır), bu metot en alt bileşenden başlanarak yukarı doğru çalıştırılır. Örnek olarak şu öğe ağacını gözönüne alalım:

 ![React Element Tree](react-element-tree.png)

Doğum süreci başladığında, `render()` metodu şu sıra ile çalışacaktır:
 
 ```
 A -> A.0 -> A.0.0 -> A.0.1 -> A.1 -> A.2.
 ```
 
`componentDidMount()` metodu ise en alt bileşenden başlayarak yukarı doğru sıra ile çalışacaktır. 
 
 ```
 A.2 -> A.1 -> A.0.1 -> A.0.0 -> A.0 -> A
 ```

Öcelikle alt bileşenler render'i tamamlayıp Native UI katmanına mount olacak ki, bir üstteki bileşen de render işlemini tamamlayıp mount olabilsin ve kendi `componentDidMount()` metodu çalışsın.

Geriye doğru ilerlerken, biliyoruz ki her alt bileşen mount oldu ve kendi `componentDidMount()` metodunu çalıştırdı. Bu sayede üst bileşen `componentDidMount()` metodunu çalıştırdığında, tüm alt bileşenlerin Native UI katmanına mount olmuş ve ulaşılabilir olması garanti edilmiş olur.

Üç bileşeni ve çağrılma sıralarını inceleyelim. 

**Torun.js**
```javascript
/** 
 * Torun
 * componentDidMount() metodunu loglar ve 
 * `value` isimli bir metoda sahiptir
 */ 
import React from 'react';
import ReactDOM from 'react-dom';

export default class Torun extends React.Component {

  componentDidMount() {
    console.log('Torun mount oldu.');
  }

  value() {
    return ReactDOM.findDOMNode(this.refs.input).value;
  }

  render() {
    return (
      <div>
        Torun
        <input ref="input" type="text" defaultValue="foo" />
      </div>
    );
  }
}
```

**Cocuk.js**
```javascript
/*
 * Cocuk
 * componentDidMount() metodunu loglar ve `value` isimli bir metoda sahiptir.
 * `value` metodu torun `value` değerini döndürür.
 */
import React from 'react';
import GrandChild from './Torun';

export default class Cocuk extends React.Component {

  componentDidMount() {
    console.log('Cocuk mount oldu.');
  }

  value() {
    return this.refs.torun.value();
  }

  render() {
    return (
      <div>
        Cocuk
        <Torun ref="torun" />
      </div>
    );
  }
}
```

**Anne.js**
```javascript
/*
 * Anne
 * componentDidMount() ve çocuk `value()` loglar
 * method.
 */
import React from 'react';
import Cocuk from './Cocuk';

export default class Anne extends React.Component {

  componentDidMount() {
    console.log('Anne mount oldu.');
    console.log('Cocuk değer:', this.refs.cocuk.value());
  }

  render() {
    return (
      <div>
        Anne
        <Cocuk ref="cocuk" />
      </div>
    );
  }
}
```

`<Anne />` bileşenini uygulamamızda render ettiğimizde, browser konsolunda şöyle bir çıktı elde ediyoruz:

```console
Torun mount oldu.
Cocuk mount oldu.
Anne mount oldu.
Cocuk değer: foo
```

Görüldüğü üzere ilk olarak Torun bileşenin `componentDidMount()` metodu çalıştırıldı, daha sonra Cocuk ve Anne. 

## Kullanım alanları

Native UI katmanıyla etkileşime geçmek için çok faydalı bir metod olduğunu yukarıda inceledik. Örnek olarak Native UI katmanı bileşenlerin çıktısını nasıl verdiğine bakarak (örneğin bir bileşenin genişliğini/yüksekliğini ölçerek) uygulama `state`ini güncellemek isteyebiliriz. Özellikle browser ile çalışırken bir çok yerleşimi CSS ile yaptığımızdan, JS tarafındaki bileşen kendi ebatlarını bilmiyor olabilir. Bu durumda ölçümler yaparak, bileşen `state`ini `componentDidMount()` metodunda güncelleyebiliriz. 

Bir diğer kullanım alanı da 3. parti kütüphane ve framework'lerin konfigüre edilmesi olarak gösterilebilir. Örnek olarak bir jQuery pluginini React bileşeni içerisinde kullanmak isteyebiliriz. Bilindiği gibi jQuery eklentileri etkin olabilmesi için genellikle belirli DOM elemanlarının seçilmesine ihtiyaç duyarlar. `componentDidMount()` metodunda bu gerekli DOM elemanlarına erişebilir ve gerekli yapılandırmayı gerçekleştirebiliriz.

Örnek olarak [C3.js](http://c3js.org/) veya [Date Range Picker](http://www.daterangepicker.com/) gibi 3. parti eklentileri yapılandırabiliriz. 
 **Chart.js**
 
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import c3 from 'c3';

export default class Chart extends React.Component {

  componentDidMount() {
    this.chart = c3.generate({
      bindto: ReactDOM.findDOMNode(this.refs.chart_div),
      data: {
        columns: [
          ['data1', 30, 200, 100, 400, 150, 250],
          ['data2', 50, 20, 10, 40, 15, 25]
        ]
      }
    });
  }

  render() {
    return (
      <div ref="chart_div"></div>
    );
  }
}
```

Yukarıdaki örnekte, `componentDidMount()` metodundan yararlanarak chart eklentisini yapılandırıyoruz. Bu işlem için `c3` kütüphanesi bir DOM elemanına (div) ihtiyaç duyuyor. Bu div'i de bileşen `render()` metodunda oluşturup, bir referansını saklıyoruz (`chart_div`). Daha sonra da bu referans ile ilgili DOM elemanını bularak `c3` yapılandırmasında kullanıyoruz. 

Genellikle 3. parti eklentileri kullanacağımız zaman olay yönetimiyle de ilgilenitiz. Örneğin kullanıcının chart ile etkileşime geçmesi gibi. `componentDidMount` metodu bahsi geçen `event`lere de kaydolmak için en doğru yerdir.


## Yeni bir render tetikleme [^1]

Bazı özel durumlarda ilk renderden sonra anında tekrar render işlemine gerek duyabiliriz. Bu sık karşılaşılan bir durum değildir ve genellikle bileşen `state` değerinin, bileşenin ilk render'den sonra nasıl Native UI katmanında görüntülendiğiyle alakalıdır. Örnek olarak bir data-table'de dinamik olarak satır yüksekliği ve stun genişliği hesaplanması verilebilir. Ya da ilk seferde ekran üzerinde nasıl konumlandıklarına bağlı olarak çocuk bileşenlerin tekrar konumlandırılması gerekliliği olabilir.  

Eğer böyle bir ihtiyaç duyarsanız, `componentDidMount()` metodu içerisinde `this.setState()` veya `forceUpdate()` metodlarını çağırabilirsiniz. Eğer bileşen `state`ini günceller ya da `forceUpdate()` metodunu çağırırsanız (daha sonra bu konuya değineceğiz), bileşen tekrar baştan bir render sürecine girer ve [Gelişme/Güncelleme safhası](../growth_update_indepth.md)'ına geçer. `componentDidMount()` yalnızca bir kere çağrıldığı için, sonsuz döngüye girilme ihtimali yoktur. 
 
***Gelecek Bölüm:*** [Gelişme/Güncelleme safhası](../growth_update_indepth.md)

---
[^1] Bu noktada dikkat edilmez ise, yine de birden fazla kez aynı bileşen render edilebilir. Bu da zaman zaman performans sorunlarına sebep olabilir.

