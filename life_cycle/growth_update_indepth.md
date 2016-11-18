# Bileşen gelişim dönemi

Bileşen bir kere doğup, bazı aşamalardan geçip, Native UI katmanı üzerne `mount` olduktan sonra, gelişme ve güncelleme safhasına geçer. Bileşenin yaşamının büyük kısmı bu evrede geçer. Bileşen `state` değerinin güncellenemesi, kullanıcı veya sistem ile etkileşime geçilmesi ve uygulamanın genel işlevinin yerine getirilmesi hep bu gelişme ve güncelleme evresinde gerçekleşir.

Gelişim süreci üç farklı şekilde tetiklenir.

- `props` değerinin değişmesi
- `state` değerinin değişmesi 
- `forceUpdate()` metodunun çağrılması

Güncellemenin yönetilme şekli de, yukarıdaki yöntemlerden hangisinin kullanıldığına göre değişkenlik gösterir. Bu değişimleri tek tek inceleyeceğiz.

Bu bölümde gelişim ve güncelleme evresinde çalıştırılan metotları, çağrılma sıralarını ve de hangi amaçla kullanılabileceklerini inceleceğiz. Genel uygulama optimizasyonuyla ilgili de birkaç öneri sunacağız.

## Güncellemenin başlatılması : `props` değişimi

Bileşeni güncellemenin ilk yöntemi `props` değerini değiştirmektir. Bir bileşen için düşünüldüğünde, kendisine verilen `props` değeri (örnek: `<Kisi isim="Ata" />`) `immutable`dır. Diğer bir değişle bileşen kendisi bu değeri değiştiremez. Eğer bileşen içerisinden `props` değerini değiştirmeyi denerseniz bir hata alırsınız.

```javascript
render() {
  this.props.yas = 32;  // bunu yapmayın!
  return (
    <div>
      { this.props.isim } (yaş: { this.props.yas })
    </div>
  );
}
```
  
> TypeError: Cannot assign to read only property 'name' of object '#&lt;Object&gt;'

Bileşen açısından `props` değeri değiştirilemez olduğu için, bileşeni yöneten üst bileşen yeni bir `props` değeri göndermek zorundadır. Üst bileşen tarafından yeni `props` değeri gönderildiğinde güncelleme evresi başlamış olur.

## Güncellemenin başlatılması : `state` değişimi

Bileşen kendi `state` değerini [^1] `this.setState()` metodu ile güncellediğinde de bileşen için güncelleme süreci başlamış olur. React ile geliştirme yapmadaki en büyük `challenge`, bileşen `state` değerinin yönetilmesidir. Birçok geliştirici bileşen için dahili bir `state` kullanmaktan tamamen kaçınmaya çalışmaktadır. Diğer bazı sistemler ([MobX](http://mobxjs.github.io/mobx/) gibi) `state` çalışma mantığını değiştirmeye çalışmaktadır. Gerçekten de `state` değeri dikkatli kullanılmadığında birçok anti-pattern'e meydan verebilir. `props` değerlerinin `state`e aktarılması gibi [^2].

`state` kullanımı ile ilgili birçok soru akla gelebilir. Ne zaman kullanılmalı? Hangi data `state` içerisinde tutulmalı hangisi tutulmamalı? Ya da hiçbir zaman `state` kullanmamalı mı? Bu soruların kesin yanıtları henüz bulunmuş değil. 

### `state`in asenkron olması

React'in dahili bileşen `state`ini nasıl yönettiğine bakalım. `setState()` metodu çağrıldıktan hemen sonra bu güncellemelerin anında `this.state` değerine yansıması beklenirken, durum böyle değildir. 'setState()' metodu asenkron bir çağrı olarak değerlendirilmelidir [^3]. 

`setState()` çağrıldığında, bu, kısmi bir güncelleme olarak değerlendirilir. Burada tüm `state` değerini değiştirmiyor sadece bazı kısımlarını güncelliyoruz. React burada kısmi güncellemeleri uygulamak için bir kuyruk yapısı kullanır [^4]. Herhangi bir metod içerisinde birden fazla kısmi güncelleme yapıyor olabiliriz. Bu güncellemeleri yönetebilmek için bir değişim-isteği kuyruğu oluşturulur. Bir kere değişim isteği kuyruğa girdiğinde, React bu bileşeni `kirli` (`dirty`) kuyruğuna alır. Bu kuyruk, dahili `state` değeri değişen bileşenleri izler. React bu sayede ileride hangi bileşen örneğinin güncelleme aşamasına geçeceğini bilir.

Bir metot içerisinde `setState()` ile güncelleme yaptıkran sonra, aynı senkron metot zinciri içerisinde `this.state` ile bileşen `state` değerine erişmeye çalışmak yapılabilecek bir hatadır. Özellikle bileşen `state` değerini dış dünyaya public bir metot (örneğin `deger()`) vasıtası ile açtıysanız. Bu konuya tekrar bu bölümün sonunda döneceğiz. 
 
## Güncellemenin başlatılması : `forceUpdate()` metodu

Bileşeni güncelleme aşamasına sokmanın bir diğer yolu da bileşen içerisinden `forceUpdate()` metodunu çağırmaktır. 
 
***Gelecek bölüm***: [Güncelleme ve `componentWillReceiveProps()` metodu](update/component_will_receive_props.md)
 
 ---
 
[^1] Bileşen `state` değeri ile bileşenin kendi içerisinde, dış ortamdan izole olarak tuttuğu veriyi kastediyoruz. Esasında teorik olarak bir bileşenin `state` değerine dışarıdan erişebilir hatta değiştirebiliriz. Ancak bu bir anti-pattern'dir ve sisteme büyük bir kırılganlık getirecektir (bileşen bağımlılıklarının artması gibi). Bir bileşen ancak kendi `state` değeri üzerinde güncelleme yapmalıdır.
 
[^2] `props` ile gelen verinin `state`'e aktarılması bir [anti pattern olarak görülse](https://facebook.github.io/react/tips/props-in-getInitialState-as-anti-pattern.html) de, birkaç kullanım alanı bulunabilir. En sık görüleni bir `defaultValue` değerinin bileşenin `state` değerine aktarılmasıdır. React içerisinde birçok form elemanında bu yapıyı görmek mümkündür. Son zamanlarda da bu yaklaşımdan uzaklaşılarak [Kontrollü form bileşenleri](https://facebook.github.io/react/docs/forms.html#controlled-components)ne doğru bir kayma görülmektedir.

[^3] React kaynak kodunda bir yorum satırında verilen tavsiye şu şekildedir: `this.state` değeri immutable olarak düşünülmelidir. `this.state` değerinin anında güncelleneceği garanti edilemez. `this.setState()` metodu çalıştırıldıktan hemen sonra `this.state` değerine ulaşmaya çalışıldığında eski `state` değeri elde edilebilir.

[^4] `enqueueSetState()` metodu.
