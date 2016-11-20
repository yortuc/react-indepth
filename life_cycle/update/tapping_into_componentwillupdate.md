# `componentWillUpdate()` metodu

Geçen bölümde bileşen için render edilip edilmeyeceğine karar vermiştik. Eğer bileşen render olacak ise `componentWillUpdate` metodu çalıştırılır. Bu metoda da yine `nextProps` ve `nextState` argümanları gönderilir. `componentWillUpdate` metodu [`componentWillMount()`](../birth/premounting_with_componentwillmount.md) metoduna benzemektedir ve birçok ortak noktası vardır. 

Aradaki fark, `componentWillUpdate`, `this.setState()` çağrıldığında olduğu gibi her render öncesi çalıştırılır. Ayrıca `componentWillMount` metounda bileşen `state` ve `props` değerlerine ulaşabilmemiz mümkündür. 

`componentWillUpdate` metodu render öncesinde çalıştırıldığı için, metot içerisinde Native UI katmanı sorgulanır ise, (örneğin bir div içerisindeki metni okuma), eski data elde edilecektir. Metot içerisinden `refs` değerine ulaşabiliyor olsak da, bu da yine, dolaylı olarak eski dataya ulaşmaya sebebiyet vereceği için çok önerilmemektedir.Ancak bazı istisnai durumlar da vardır. Örneğin animasyon başlatılması gibi durumlarda, render öncesi Native UI üzerine müdahale edilebilmektedir.

`componentWillUpdate` metodu bize gelecek render öncesi hazırlık yapma imkanı tanır. Eğer eski `props` ve `state` değerlerine `this.props` ve `this.state` ile ulaşabilmek mümkündür. Gelecek render için de `nextProps` ve `nextState` değerleri de metoda React tarafından argüman olarak gönderilmektedir. Bu sayede değişen/değişmeyen değeler karşılaştırılarak gerekli hesaplamalar yapılabilir.

`componentWillMount` metodunun aksine, `componentWillUpdate` içerisinde `this.setState()` çağrılmamalıdır. Çünkü `setState` metodu yeni bir `componentWillUpdate` metodu tetikleyecektir. Bu sayede sonsuz bir döngüye girilmiş olur [^1].

Özetle `componentWillUpdate` metodunun en yaygın kullanım alanı state değişimi olduğunda bir değişkenin değerini değiştirme (`this.setState` kullanmadan), olay yayınlama (dispatching events) ve animasyon başlatma olarak gösterilebilir [^2].

```javascript
// state değişiminde aksiyon alma
componentWillUpdate(nextProps, nextState) {
  if (nextState.open == true && this.state.open == false) {
    this.props.onWillOpen();
  }
}
```

***Gelecek bölüm*** [Yeniden render etme ve alt bileşen güncellemeleri](rerendering_and_children_updates.md)

---

[^1] `componentWillUpdate` metodu içerisinde teknik olarak `setState` çalıştırılabilir. Bir `eğer` ifadesi kullanılarak burada sonsuz döngü oluşması engellenebilir. Ancak yine de önerilmemektedir ve kaçınılması gerekir. Bu uyarıyı yaptığı içi [Robin Venneman](https://github.com/robinvenneman)'a teşekkürler.

[^2] `componentWillUpdate` içerisinde bir css animasyonu başlatılmasıyla ilgili örnek için şu [stackoverflow cevabına](http://stackoverflow.com/a/33075514/815544) bakabilirsiniz.
