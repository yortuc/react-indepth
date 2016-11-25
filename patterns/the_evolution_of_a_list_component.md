# Bir liste bileşeninin evrimi

Günümüzde uygulamalarda liste bileşenlerine her yerde yaşanabilmektedir. Örneğin Twitter, Facebook, Reddit, Instagram gibi sosyal medya uygulamalarının arayüzünün bel kemiğini listeler oluşturmaktadır. Bu açıdan, bir liste bileşeni oluşturma aşamalarını incelemek iyi bir pratik olabilir.

## İlk deneme

İlk etapta yapılacak iş ihtiyacımızı karşılayacak arayüzü kabacak render bir React bileşeni oluşturmaktır. Örneğimizde bir müşteri profilleri listesi oluşturacağız. İlk tasarım taslağında müşteri için bir profil resmi ve ad-soyad gösterecek bir metin alanı yeralacak.
 
**Arayüz taslağı #1**

![Basit bir profil](react-indepth-avatar-list.png)

İlk adım profil resim url'ini ve açıklayıcı metni (ad-soyad) içeren nesnelerden oluşan bir array kabul eden React bileşenini oluşturmaktır. Bileşenimiz bu array üzerinde bir döngü ile ilerleyecek ve her bir elemanı `<li>` kullanarak render edecek.


```javascript
import React from 'react';

class Liste extends React.Component {
  profilleriRenderEt () {
    return this.props.profiller.map( (profil) => {
      return (
        <li>
          <img href={ profil.resimUrl } align="left" width="30" height="30" />
          <div className="profil-aciklama">
            { profil.aciklama }
          </div>
        </li>
      );
    });
  }

  render() {
    return (
      <ul className="profil-liste">
        { this.profilleriRenderEt() }
      </ul>);
  }
}

Liste.defaultProps = { profil: [] };
export default Liste;
```

Daha sonra tasarım ihtiyacını karşılamak için  `<ul>`, `<li>` ve `<div>` elementlerine stil veriyoruz. Bu bileşen ihtiyacımızı şimdilik karşılıyor, gerekli içeriği render ediyor ancak tekrar kullanılabilir değil.


## İhtiyaçların değişmesi

Her proje zaman içerisinde değişime ihtiyaç duyar. Örneğimizde, kullanıcılar bir profil için daha fazla bilgi görmek istiyor olabilir. 

**Arayüz taslağı #2**
 
![İsteğe bağlı detaylar](react-indepth-details-list.png)
 
Bu ilk tasarım değişikliği ile beraber, bileşen üzerinde refactor işlemine başlamış oluyoruz. Yeni eklenen isteğe bağlı alanları render edebilmek için bileşen render meetodunda revizyona gitmemeiz gerekiyor. Bileşenleri ne kadar küçük be birbirinden bağımsız tasarlamamanın birçok faydası vardır. Öncelikle zihinsel yükü azaltmış oluruz. Küçük bileşenlerin kodunu okumak, anlamak ve revize etmek daha kolay olacaktır. Altı ay sonra, yazdığımız koda döndüğümüzde, o anki niyetleri ve düşünceleri hatırlmak, hele ki bileşenler karmaşık mantık içeriyor ise ciddi zaman alabilir. 

React'in en güzel özelliklerinden birisi de bileşenleri daha küçük parçalara ayırabilme imkanı vermesidir. Kodu daha anlaşılır kılacağı gibi, bileşenlerin tekrar kullanabilirliğini de artıracaktır.

Bu doğrultuda her bir bileşen sadece tek bir iş yapacak şekilde tasarlanmalıdır. Yukarıda oluşturduğumuz liste bileşeni, profilleri alarak tek tek render etmektedir. Bir profilin render edilmesi başka bir bileşen tarafından yapılırsa, bu bileşeni daha sonra da kullanabiliriz. Profil render işlemi için yeni bir bileşen oluşturalım ve önceki bileşeni refactor edelim.

 
## Profil bileşeni
 
 **Profil.js**
```javascript
import React from 'react';

export default class Profil extends React.Component {
  renderDetails(key, label){
    if (this.props[key]) {
      return (<div className="detail">{ label } { this.props[key] }</div>);
    }
  }

  render() {
    return (
      <li>
        <img href={ this.props.imagePath } align="left" width="30" height="30" />
        <div className="profile-description">
          { this.props.description }
        </div>
        { this.renderDetails('email', 'Email:') }
        { this.renderDetails('twitter', 'Twitter:') }
        { this.renderDetails('phone', 'Phone:') }
      </li>
    );
  }
}
```

Burada profil bilgilerini render etme işlemini `Liste` bileşeninden çıkartarak, yeni oluşturduğumuz `Porfil` bileşenine veriyoruz. İsteğe bağlı bilgilerin render edilmesi işini de bu bileşene devrediyoruz.

 **Liste.js**

```javascript
import React from 'react';
import Profile from './Profile';

class Liste extends React.Component {
  render() {
    return (
      <ul>
        { this.props.profiller.map( (profil) => <Profil {...profil} /> ) }
      </ul>
    );
  }
}

Liste.defaultProps = { profil: [] };
export default List;
```

Şimdi `Liste` bileşenimiz kendisine verilen `profiller` datasını itere ederek `Profil` bileşenine aktarıyor. Profil render işleminin listeden bağımsız ayrı ve izole bir bileşen tarafından yapılması sayesinde net bir [separation of concerns (SoC)](https://en.wikipedia.org/wiki/Separation_of_concerns) yapısı elde etmiş oluyoruz.

***Gelecek bölüm***: [Farklı içerik render etme](rendering_different_content.md)
 
 ---
 
 [^1] Bu mantık ile daha da ileri gidebiliriz. Profil bileşenindeki detayların da farklı bileşen tarafından render edilmesini sağlayabiliriz. Ancak bu da `granularity` noktasında abartı olacaktır. Daha önce bahsettiğimiz gibi, uygulama yapısını karmaşıklaştıran her etken bize zarar vermektedir.
 P