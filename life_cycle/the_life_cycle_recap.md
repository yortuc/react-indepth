# Yaşam döngüsü özet

Bir bileşen için üç farklı yaşam döngüsü evresi inceledik. 

1. [Doğum/Mounting](birth_mounting_indepth.md)
2. [Gelişme/Güncelleme](growth_update_indepth.md) 
3. [Ölüm/Unmount](death_unmounting_indepth.md)

Bu süreçler ve çağrılmas sırası değişmez olan metorlar sayesinde React bize bileşen geliştirebilmek için çok net bir yol çizmiş oluyor. Bu sayede bileşenler ve genel uygulama performansı üzerinde optimizasyon yapabilmemize olanak sağlıyor. 

Hatırlamak gerekirse bu metotlar sırası ile:

## Doğum / Mounting
1. İlk yapılandırma (initialization)
2. `getDefaultProps()` *(React.createClass)* veya `Bilesen.defaultProps` *(ES6 class)*
3. `getInitialState()` *(React.createClass)* veya `this.state = ...` *(ES6 constructor)*
4. `componentWillMount()`
5. `render()`
6. Alt bileşenlerin init edilmesi ve yaşam döngüsüne başlatılması
7. `componentDidMount()`
  
## Gelişme / Güncelleme
1. `componentWillReceiveProps()`
2. `shouldComponentUpdate()`
3. `componentWillUpdate()`
3. `render()`
4. Alt bileşen yaşam döngüsü
5. `componentDidUpdate()`

## Ölüm / Un-Mounting
1. `componentWillUnmount()`
4. Alt bileşen yaşam döngüsü 
5. Bileşen örneğinin Garbage Collection için hazır edilmesi

## Yaşam döngüsü grafiği ve `setState()` güvenliği

Ek olarak, [Peter Beshai](https://twitter.com/pbesh) tarafından oluşturulmuş bu grafik üzerinde hangi evrelerde `setState` metodu kullanmak güvenlidir bunu görebiliyoruz.

![Lifecycl Flow Chart](react-lifecycle-flow-chart-states.png)

---

 ***Gelecek bölüm:*** [Bileşen evrimi ve kompozisyon](the_life_cycle_recap.md) 
