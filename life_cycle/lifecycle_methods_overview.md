# Yaşam Döngüsü Metodları 
Bileşenin yaşan döngüsü boyunca olan olaylara göre aksiyon alabilmemiz için React bize metodlar sunmaktadır. Bu metodlar sayesinde bileşenin kendi yaşam döngüsü içerisinde ne aşamada olduğunu anlayabilir ve bileşenin ne yapıp yapamayacağını belirleyebiliriz. 

Her bir yaşam döngüsü metodu belirli sıra ile çalıştırılmaktadır. Burada yaşam döngüsü metotları sıralı olarak verilmiştir.

## Doğum / Mounting
1. İlk kullanıma hazırlama (initialization) / Yapılandırma (Construction)
2. `getDefaultProps()` *(React.createClass)* veya `Bilesen.defaultProps` *(ES6 class)*
3. `getInitialState()` *(React.createClass)* veya `this.state = ...` *(ES6 constructor)*
4. `componentWillMount()`
5. `render()`
6. Çocuk bileşenlerin yapılandırılması
7. `componentDidMount()`
  
## Gelişme / Güncellenme
1. `componentWillReceiveProps()`
2. `shouldComponentUpdate()`
3. `componentWillUpdate()`
3. `render()`
4. Çocuk bileşenlerin yaşam döngüsü metodları (gerekli ise doğum, güncelleme, ölüm)
5. `componentDidUpdate()`

## Ölüm / Unmount
1. `componentWillUnmount()`
4. Çocuk bileşenlerin yaşam döngüsü metodları
5. Bileşen örneğinin (instance) Garbage Collection için serbest bırakılması

Bu metotların çağrılması sırası kesin olarak yukarıdaki sıra ile olmaktadır. Bileşen hayatının büyük kısmı gelişme/güncellenme aşaması olarak geçmektedir ve bu aşamadaki metotlar birçok kere çağrılır. Buna karşılık doğum ve ölüm metotları sadece birer kere çalıştırılır.

### ES5 ve ES6 Arasındaki Farklar

Bileşen tanımlamasında ES6 veya mevcut javascript (ES5) kullanımına bağlı olarak bazı küçük farklılıklar görülebilir. Örnek olarak ES6 kullanıldığında düz javascript class'ları ile tanımlama yapılır:

```jsx
class ParagrafBileseni extends React.Component {
	constructor(props) {
		super(props);
		this.state = {tiklanmaSayisi: 0};
	} 
	render = ()=> <p className="paragraf">{this.props.metin}<p>
}

ParagrafBileseni.defaultProps = { metin: "lorem ipsum" };

```

eğer ES5 kullanılıyor ise React üzerinde tanımlı bir yardımcı metot `createClass` çağrılarak bileşen oluşturulur:

```jsx
var ParagrafBileseni = React.createClass({
	getInitialState: function() {
		return {tiklanmaSayisi: 0};
	},
	getDefaultProps: function() {
		return {metin: "lorem ipsum"};	
	},
	render: function() {
		return <p className="paragraf">{this.props.metin}<p>
	}
});
```

Bunlar dışında özellike bileşen "init" edilme noktasında property'lerin ve başlangıç state'i tanımlada da bazı farklılıklar olduğu yukarıdaki örnekte görülmektedir. Bu konuya tekrar değineceğiz.

---

***Gelecek Bölüm***: [Doğum/Mounting Aşaması](birth_mounting_indepth.md)