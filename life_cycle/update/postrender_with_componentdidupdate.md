# `componentDidUpdate()` metodu ve render sonrası

`componentDidMount` metodunun güncelleme evresi için olanı şeklinde düşünülebilir. NativeUI katmanına ulaşabilir, `refs` ile render edilmiş olan Native elementlere müdahale edebilir ya da yeni bir `render` süreci başlatabiliriz. 

`componentDidUpdate` çalıştırılırken `prevProps` ve `prevState` değerleri argüman olarak gönderilir. Argümanların isminde de anlaşılabileceği gibi bunlar eski değerlerdir. Mevcut `props` ve `state` değerlerine `this.props` ve `this.state` ile ulaşılabilir.

![](react-tree-update.png)

`componentDidMount` ve `componentDidUpdate` metotlarında olduğu gibi, ancak bileşenin tüm alt bileşenleri güncelleme işlemini tamamladıktan ve `componentDidUpdate` metotlarını çalıştırdıktan sonra bu metot çalıştırılır. 

Hatılatmak gerekirse ilk olarak **A.0.0** bileşeninin `componentDidUpdate` metodu çalıştırılacak, daha sonra  **A.0** ve son olarak **A**.

## Genel kullanım 

En genel kullanım alanı NativeUI katmanına mudaha etmesi gereken 3. parti kütüphanelerin gerekli işlerini yapabilmesidir. 3. parti kütüphane kullanıldığında, daha önceki Chart örneğinde olduğu gibi kütüphanenin yeni data ile çizimi güncellemesi gerekir. 

```javascript
componentDidUpdate(prevProps, prevState) {
  // grafiği sadece veri güncellendi ise tekrar çiz
  if (prevProps.data !== this.props.data) {
    this.chart = c3.load({
      data: this.props.data
    });
  }
}
```

Burada Chart örneğine erişiyor ve yeni data ile güncelliyoruz [2]. 

### Diğer bir `render` çalıştırılması

Aynı zamanda burada NativeUI katmanını sorgulayarak element boyutlarını elde edebiliriz. Bu bilgi doğrultusunda bileşenin `state` değerini veya alt bileşenlerin `props` değerlerini güncellemek isteyebiliriz. Ancak burada direk olarak `setState` veya `forceUpdate` çalıştırır isek bileşen render edildikten sonra tekrar `componentDidUpdate` çalışacağı için sonsuz bir döngüye girilmiş olur. React geliştirmede yapılabilecek en büyük yanlışlardan birisi kontrolsüz bir `setState` kullanımıdır.


```javascript
componentDidUpdate(prevProps, prevState) {
  // bunu yapmayın!
  let height = ReactDOM.findDOMNode(this).offsetHeight;
  this.setState({ internalHeight: height });
}
```

Varsayılan olarak `shouldComponentUpdate` `true` döndürür. Bu yüzden eğer yukarıdaki kodu denersek, sonsuz bir render döngüsüne girilir. 

Bunu bir kontrol ile önlemek mümkündür. Örneğin burada `componentDidUpdate` içerisinde, eğer ebatlar gerçekten değişmiş ise `state` ataması yapılması, döngüye girilmesini engeller.

```javascript
componentDidUpdate(prevProps, prevState) {
  let height = ReactDOM.findDOMNode(this).offsetHeight;

  // yükseklik değeri gerçekten değişti ise 
  if (this.state.height !== height ) {
    this.setState({ internalHeight: height });
  }
}
```

Daha önce de belirtildiği gibi gereksiz render'ler uygulama performansını kötü olarak etkiler. Diğer bir render tetiklemeden önce bunun gerekli olup olmadığı iyi araştırılmalıdır.

***Gelecek bölüm*** [Bileşen ölümü](../death_unmounting_indepth.md)

---
 
 [^1] Sonsuz döngüye girilebilir.
 
 [^2] Burada datanın immutable veya mutate edilmemiş olduğunu kabul ediyoruz.

  