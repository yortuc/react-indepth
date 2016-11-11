# Doğum / Mounting 

Bir React bileşeni hayatına uygulama başlangıcındaki `ReactDOM.render()` metodunun çağrılması ile başlar. Doğum sürecinin temel odak noktası bileşenin başlangıçtaki durumunun (state) yapılandırılmasıdır. Bu aşamada bileşene dışarıdan gönderilen props değerleri ile ilk yapılandırılma başlamış olur.

 ```javascript
class Bilesen extends React.Component {
	constructor(props) {
		super(props);
		this.state = {metin: "başlangıç metni"};
	}
	render = ()=> <span>{this.props.metin}</span>
}
 ```
 
Yukarıda bir bileşenin hayata başlama sürecinde öncelik props değerlerinin dışarıdan enjekte edilmesini ve hemen sonra da başlangıç state değerinin atanmasını görüyoruz.

Props ve state atamaları yapıldıktan sonra, rendering aşamasında çocuk bileşenlerin her biri için sırayla yaşam döngüsü işletilmeye başlanır. Çocuk bileşenler de Native UI katmanına [^1] mount olduktan sonra, bileşenimiz de Native UI'e erişebilme aşamasına gelir. Bu sayede browser üzerinde çalışıyor isek DOM'u sorgulayabilir ve bileşenin düzgün render edilip edilemediğini anlayabiliriz. Aynı zamanda 3. parti eklenti ve kütüphaneleri de init etmeye bu aşamada başlayabiliriz.
 
## Bileşenler ve Öğeler

React öğrenmeye başlayanlar için kafa karıştırıcı olabilecek bir nokta vardır. İlk bakışta birisi Native UI katmanına mount olan bileşen örneğinin bileşen class'ı ile aynı şey olduğunu düşünebilir. Örneğin basit bir React bileşeni oluşturup, Native UI katmanı olarak browse (DOM) üzerine render edersek:

 ```javascript
 import React from 'react';
 import ReactDOM from 'react-dom';
 
 class Bilesen extends React.Component {
   render() {
     return <div>Hello World!</div>;
   }
 };
 
 ReactDOM.render(<Bilesen />, document.getElementById('moun-edilecek-div')); 
 ```

İlk etapta render metodunda `Bilesen` isimli class'ın bir örneği oluşturuluyor sanılabilir. `new Bilesen()` dermiş gibi. Daha sonra bu class örneği (instance) ReactDOM.render metoduna verilerek DOM üzerine render ediliyor diye düşünülebilir. 

Gerçekte olan ise JSX işlemcisi `<Bilesen />` satırını görüp `React.createElement` metoduna parametre olarak verir. Render edilmek üzere verilen değer aslında class örneği değil, bu fonksiyondan dönen React Öğesidir.
 
 ```javascript
 // JSX işlemcisinden sonra 
 ReactDOM.render(
   React.createElement(Bilesen, null), document.getElementById('mount-edilecek-div')
 );
 ```

 Yani `React.createElement(Bilesen, null)` fonksiyon çağrısından geriye dönen değer bir React Öğesidir. React Öğesi[^2], React tarafından Native UI katmanında bu bileşenin nasıl görüntüleneceğini ve nasıl davranacağını tarif eden basit bir veri yapısıdır. Sanal DOM'un yapıtaşı bu React öğeleridir.
 
 
> React'teki esas veri tipi ReactElement isimli öğedir. Sadece dört özellik (property) barındırır: type, props, key ve ref. React öğesi hiçbir metot ve fonksiyonalite barındırmayan salt veridir.
>
> -- https://facebook.github.io/react/docs/glossary.html#react-elements

Sonuç olarak her bir React bileşeni, render metodunda, aslında sanal DOM verisi döndürmektedir. 
 
## İlk `render()`
En bilinen bileşen metodu şüphesiz `render()` metodudur. JSX notasyonunu genellikle burada kullanırız. Uygulamanın nasıl görüneceğini büyük ölçüde render'da belirleriz. İlk render ise normal render metodunun biraz özel bir hali olup tüm uygulamayı ilk seferde Native UI katmanına mount eder, yerleştirir.

Browser ortamında bu `ReactDOM.render()` metodudur. Kök bileşeni bu metoda verip, render'in nereye (hangi html elemnaı içerisine) yapılacağı söylenir. Bu metod çağrısı ile React kendisine verilen Öğeleri (ReactElement) işlemeye başlar ve bu öğelere bakarak her bir bileşenin (Component) örneğini oluşturur. Öğeye bakarak hangi tip bileşen oluşturmasını gerektiği React anlayabilir, ve daha sonra `pros` değerleri öğeden bileşen örneğine aktarılır.  

Tam bu sırada bileşen yaşam döngüsü başlamış olur. Öğe üzerindeki `instance` özelliği ile React her bir öğe için bileşen örnekleri oluştrmaya başlar.
 
 ***Gelecek Bölüm:*** [İlk yapılandırma](birth/initialization_and_construction.md)

---
[^1] Native UI katmanı, arayüz elemanlarının ekran üzerine çizdirilmesinden sorumlu sistemdir. Browser içerisinde çalışıyor isek Native UI katmanı DOM'a tekabül eder. Mobil bir uygulamada ise UIView (veya Android muadili) olarak karşımıza çıkar. React bileşeni ile Native UI üzerindeki arayüz elemanları arasındaki eşleşmeyi React kendisi yapar. Örnek olarak bir bileşenin bir yerindeki metni değiştirdiğinzde, eğer DOM üzerinde çalışıyorsanız React bu DOM güncellemesini kendisi yapar. 

[^2] Dan Abramov'un bu konudaki yorumlarına gözatabilirsiniz.
http://stackoverflow.com/a/31069757
