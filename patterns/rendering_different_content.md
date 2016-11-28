# Farklı içerik render etme

Profillerin render edilmesini müstakil bir bileşene devrederek listenin yerleşimini ve içeriğini ayırmış oluyoruz. Liste bileşeni, listenin yerleşimini ve veri yönetimi idare eder bir hale gelirken, liste elemanlarını (profil) kendi bağımsız bileşeni istediği şekilde render ediyor. Bu yapının da katkısı ile daha da esnek bir liste bileşeni elde etmemiz mümkün.

 
## Liste özellikleri

Liste bileşenimizin, yazılım geliştikçe daha da farklı ihtiyaçları doğduğunu düşünelim. Sayfalama, seçim yönetimi, sıralama, filtreleme gibi özellikler gerekebilir. Daha da ötesi, kullanıcılar farklı listelere ihtiyaç duyabilir. Örneğin blog gönderilerinin listelenmesi gibi.

Blog gönderileri de profil gibi birer resim, açıklama ve detay özelliklerine sahipler. Ancak içerik ve bu içeriğin yerleşimi tamamen profilden farklı. Burada Liste bileşeninin sahip olduğu sayfalama, sıralama, filtreme vb. tüm özellikleri kullanma devam ederken, aynı zamanda profil yerine blog gönderileri göstermek istiyoruz. Bunu nasıl sağlayabiliriz?
 
## Eleman render etme

İdeal olmasa da pratik bir metot, liste bileşeninin `map` metoduna bir `switch` eklemek olabilir. `switch`, liste elemanının veri türüne bakaradak render etmek için profil bileşenini ya da blog gönderisi bileşenini seçebilir. Ancak bu yöntem kodumuzda bir [code smell](https://en.wikipedia.org/wiki/Code_smell)'e sebep olur. Aynı ilk Liste bileşen tasarımında olduğu gibi, bu yöntem şuanki sorunumuzu çözse de, ileride yeni bir liste elemanı bileşeni ortaya çıktığında (Mesaj gibi) ne olacak? Liste bileşeni birçok `switch` kontrolü içermek zorunda kalacaktır.

Daha iyi bir çözüm ise konfigürasyon ile bu problemi çözmek olabilir. Liste bileşeni üzerinde bir `prop` vasıtası ile listenin her elemanı için ne tür bir bileşenle render edilmesi gerektiği belirtileiblir. 

 A better way to solve this is through configuration. We can expose a prop on the List component that handles the rendering of each item. There are two ways to do this: by passing in a function or by passing in a Component Class.


### Eleman render etme fonksiyonu

İlk deneme olarak, her bir elemanın nasıl render edilmesi gerektiğini kontrol edecek bir fonksiyonu liste bileşenine bir `prop` olarak vereceğiz. 

**Liste.js**
```javascript
import React from 'react';

class Liste extends React.Component {
  render() {
    return (
      <ul>
        { this.props.items.map( (item, index) => this.props.itemRenderer(item, index) ) }
      </ul>
    );
  }
}

List.propTypes = {
  items: React.PropTypes.array,
  itemRenderer: React.PropTypes.func.isRequired 
};
List.defaultProps = { items: [] };
export default List;
```

Bileşen konfigurasyonunda `itemRender` isimli bir fonksiyonun bileşene tanımlanmasını zorunlu ılıyoruz. Bir diğer değişiklik de, profiller yerine daha genel olacak şekilde `items` isimli bir array tanımlıyoruz. Liste bileşeni render metodunda artık her bir eleman için, props aracıığı ile verilen fonksiyonu çalıştırıyoruz. 

**index.js**
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import List from './components/List';
import Profile from './components/Profile';
import Posts from './components/Posts';

let profileData = [ ... ] // profil datası
let postsData = [ ... ]   // blog gönderileri

class App extends React.Component {
  renderProfile(profile, key) {
    return (<Profile {...profile} key={ key } />);
  }
  
  renderPosts(posts, key) {
    return (<Posts {...post} key={ key } />);
  }
  
  render() {
    return (
      <div>
        <List items={ profileData } itemRenderer={ this.renderProfile } />
        <List items={ postsData } itemRenderer={ this.renderPosts } />
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById('mount-point'));
```

`index.js` içerisinde iki farklı Liste bileşeni kullandık. Birincisinde profil datasını ve ikincisinde ise blog gönderilerini render ediyoruz. Bu data array'lerinin hangi liste elemaı bileşeni ile render edileceğini ise `itemRenderer` metodu ile bileşene enjekte ediyoruz. Liste bileşeni bünyesindei `map` metodunda, kendisine `prop` vasıtası ile verdiğimiz `renderProfil` veya `renderPosts` fonksiyonun çağırarak o elemana özel render işlemi gerçekleştiriyor.

### `Key` kullanımı ve bileşen array'leri

`itemRenderer` metodunun bir de `index` argümanı aldığını görüyoruz. Listede her bir eleman için `unique` olacak şekilde bir anahtar değerine ihityaç duyulur. Render işlemini burada olduğu gibi başka bir fonksiyona devrettiğimizde, React bu `key` değerlerini bizim için otomatik olarak oluşturamaz. 

Anahtar değerleri `Reconciliation` aşaması için gereklidir

> Reconciliation (uzlaşma) aşamasında React, sanal dom çıktısı ile gerçek DOM'u günceller. Esas sorun dinamik olarak oluşturulmuş olan (liste bileşeninde olduğu gibi) alt bileşenlerin render edilmesinde ortaya çıkar. Alt bileşenlerin sıralaması değişmiş olabilir (liste filtreleme gibi durumlarda) ya da yeni alt bileşenler eklenmiş / kaldırılmış olabilir, bu durumda her bir alt bileşeni bir anahtar (`key`) ile tanıyabilir ve düzgün bir şekilde render edebiliriz.

> -- [React Al bileşen uzlaşması](https://facebook.github.io/react/docs/multiple-components.html#child-reconciliation) 

Eğer dinamik olarak (genellikle bir array üzerinde `map` meotdu ile) oluşturulmuş alt bileşenler için, bileşene özel `key` değeri tanımlamaz isek bir uyarı mesajı ike karşılaşırız:

> Warning: Each child in an array or iterator should have a unique "key" prop. Check the render method of `List`. See https://fb.me/react-warning-keys for more information.

Hızlı bir çözüm, her elemanın `index` (array içerisindeki sıra numarası gibi düşünebiliriz) değeri üzerinden bir `key` oluşturmaktır. Ancak bu çözüm ideal değildir. Listeye yeni eleman eklenip çıkarıldığında her bir elemanın liste içerisindeki sıra numarası değişecektir, aynı sıra numarası iki durumda iki farklı liste elemanına işaret edecektir. Oysa ki `key` değeri her render işleminde bir bileşen için ayırdedici olmalıdır. Data içerisinde tanımlı (örneğin db kayıtlarından gelen tablo id değeri) id değerleri kullanmak daha sağlıklıdır. Ya da her elemanı kendi iç datasına göre `hash`leyip, o elemana özel bir `key` değeri türetmek de, ilk yönteme göre daha sağlıklı olur.

Elemanın liste içerisindeki konumunua bağlı bir `key` değeri yerine, eleman datasına bağlı bir `key` değerine sahio olmak, bileşen render işlemini optimize etmede çok işimize yarar. Örneğin kısmi bir liste oluşturmak istediğimizde (listeyi filtreleyip sadece belirli kritere uyan elemanları listelediğimiz gibi), React tam olarak hangi elemanı tekrar render etmesi gerektiğini hangisi olduğu gibi kullanması gerektiğini bilebilir.

### Bileşen tipi ile eleman render etme

Dinamik olarka liste elemanlarını render etmenin bir diğer yolu da bileşen class referansı vermektir. 
 
 **Liste.js**
```javascript
import React from 'react';
import Profile from './Profile';

class Liste extends React.Component {
  render() {
    return (
      <ul>
        { this.props.profile.map( (profile, index) => {
            let newProps = Object.assign({ key: index }, profile);
            return React.createElement(this.props.itemRenderer, newProps);
        }) }
      </ul>
    );
  }
}

List.propTypes = { itemRenderer: React.PropTypes.func };
List.defaultProps = { profile: [], itemRenderer: Profil };
export default List; 
```

Buradaki liste bileşeni her bir liste elemanı için `itemRenderer` prop'unda belirtilen bileşeni kullanmaktadır. Bu yöntemde bileşen isimleri dinamik olarak verilmekte ve liste elemanları istenilen bileşen ile render edilebilmektedir. Ayrıca varsayılan değer olarak `Profil` bileşenini tanımlıyoruz, bu sayede profil render etmek istediğimizde ayrıca `itemRenderer` prop'unu tanımlamaya gerke duymamış oluyoruz.

Örnek bir kullanım şu şekilde olabilir:
 
**index.js**
 ```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import List from './components/List';
import Profile from './components/Profil';
import Post from './components/Post';

let profileData = [ ... ] // profil datası array'i
let postsData = [ ... ]   // blo gönderileri

class App extends React.Component { 
  render() {
    return (
      <div>
        <List items={ profileData } />
        <List items={ postsData } itemRenderer={ Post } />
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById('mount-point'));
```

Liste bileşeni render metodunda her bir liste elemanı için `itemRenderer` prop'unda verdiğimiz bileşeni kullanarak render işlemini gerçekleştiriyor. Bu sayede tek bir liste bileşeni ile hem profil hem de post (ve de ilerinde ekleyebileceğimiz her türlü alt bileşeni) render edebiliyoruz. Tercih meslesi olsa da, bu yöntemin fonksiyon verme yönteminden daha temiz olduğu söylenebilir.


***Gelecek bölüm***: [Yüksek derece bileşenler](higher_order_components.md)
