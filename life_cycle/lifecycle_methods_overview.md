# React Life Cycle Methods Overview
Bileşenin yaşan döngüsü boyunca olan olaylara göre aksiyon alabilmemiz için React bize metodlar sunmaktadır. Bu metodlar sayesinde bileşenin kendi yaşam döngüsü içerisinde ne aşamada olduğunu anlayabilir ve bileşenin ne yapıp yapamayacağını belirleyebiliriz. 

Her bir yaşam döngüsü metodu belirli sıra ile çalıştırılmaktadır. Burada yaşam döngüsü metotları sıralı olarak verilmiştir.

## Doğum / Mounting
1. Initialize / Construction
2. `getDefaultProps()` *(React.createClass)* or `MyComponent.defaultProps` *(ES6 class)*
3. `getInitialState()` *(React.createClass)* or `this.state = ...` *(ES6 constructor)*
4. `componentWillMount()`
5. `render()`
6. Children initialization & life cycle kickoff
7. `componentDidMount()`
  
## Gelişme / Güncellenme
1. `componentWillReceiveProps()`
2. `shouldComponentUpdate()`
3. `componentWillUpdate()`
3. `render()`
4. Children Life cycle methods
5. `componentDidUpdate()`

## Ölüm / Unmount
1. `componentWillUnmount()`
4. Children Life cycle methods
5. Instance destroyed for Garbage Collection

Bu metotların çağrılması sırası kesin olarak yukarıdaki sıra ile olmaktadır. Bileşen hayatının büyük kısmı gelişme/güncellenme aşaması olarak geçmektedir ve bu aşamadaki metotlar birçok kere çağrılır. Buna karşılık doğum ve ölüm metotları sadece birer kere çalıştırılır.

***Gelecek Bölüm***: [Doğum/Mounting Aşaması](birth_mounting_indepth.md)

---
Bileşen tanımlamasında ES6 veya mevcut javascript (ES5) kullanımına bağlı olarak bazı küçük farklılıklar görülebilir. Örnek olarak ES6 kullanıldığında düz javascript class'ları ile tanımlama yapılır:

`class ParagrafBileseni extend React.Component {
	render = ()=> <p className="paragraf">{this.props.metin}<p>
}`

eğer ES5 kullanılıyor ise React üzerinde tanımlı bir yardımcı metot çağrılarak bileşen oluşturulur:

`React.createClass({
	render: function() {
		return <p className="paragraf">{this.props.metin}<p>
	}
});`

Bunlar dışında özellike bileşen "init" edilme noktasında bazı farklılıklar görülmektedir. Bu konuya tekrar değineceğiz.