# `shouldComponentUpdate()` metodu

Bu metot sayesinde bileşen, eğer yeni bir `render`e gerek yok ise güncelleme evresinden çıkarılabilmektedir. Varsayılan değer olarak `true` döndürür ve böylece bileşen her güncelleme evresinde yeniden render edilir. Hatırlarsanız React `props` değerlerini [derinlemesine karşılaştırmaz](https://facebook.github.io/react/blog/2016/01/08/A-implies-B-does-not-imply-B-implies-A.html). Eğer `props` veya `state` değişir ise, tekrar render edilmesi gerektiğine hükmeder. Ama eğer `props` ya da `state` gerçekten değişmedi ise, tekrar render etmeye gerek var mı?

## Gereksiz render'leri önleme

`shouldComponentUpdate` metodu ilk gerçek optimizasyonların yapılabildiği yaşam döngüsü metodudur. Metot içerisinde mevcut `props` ve `state` değerleri ile yeni aktarılan değerlere erişebiliriz. Burada kendi özellştirilmiş karşılaştırmamızı yapabilir ve bileşenin tekrar render edilip edilmemesi gerektiğine karar verebiliriz. 

[PureRenderMixin](https://facebook.github.io/react/docs/pure-render-mixin.html) de tam olarak bu işi yapmaktadır. Mevcut `state` ve `props` değerlerini aktarılanlar (nextProps, nextState) ile karşılaştırarak gerçekten aynı olup olmadıklarını kontrol eder. Eğer değerlerde değişiklik var ise `true` döndür, değerler değişmedi ise `false` döndürerek gereksiz bir render önlenmiş olur.
 

 ```javascript
/*
 * yüzeysel karşılaştırma ile iki nesnenin key ve value'ları aynı mı kontrol et
 */
function shallowEqual(objA: mixed, objB: mixed): boolean {
  if (objA === objB) {
    return true;
  }

  if (typeof objA !== 'object' || objA === null ||
      typeof objB !== 'object' || objB === null) {
    return false;
  }

  var keysA = Object.keys(objA);
  var keysB = Object.keys(objB);

  if (keysA.length !== keysB.length) {
    return false;
  }

  // A'nin `key` değerleri farklı mı?
  var bHasOwnProperty = hasOwnProperty.bind(objB);
  for (var i = 0; i < keysA.length; i++) {
    if (!bHasOwnProperty(keysA[i]) || objA[keysA[i]] !== objB[keysA[i]]) {
      return false;
    }
  }

  return true;
}

function shallowCompare(instance, nextProps, nextState) {
  return (
    !shallowEqual(instance.props, nextProps) ||
    !shallowEqual(instance.state, nextState)
  );
}

var ReactComponentWithPureRenderMixin = {
  shouldComponentUpdate: function(nextProps, nextState) {
    return shallowCompare(this, nextProps, nextState);
  },
};
 ```
 
Yukarıdaki kod React addon/source'tan alınmıştır [^1]. Buradaki mixin shouldComponentUpdate(nextProps, nextState) override edererek, mevcut ve gelecek `props` ile `state` değerlerini yüzeysel bir karşılaştırma ile (shallow comparizon) aynı olup olmadıklarını kontrol eder. Eğer aynı iseler, `false` döndürerek gereksiz bir render'in önüne geçmiş olur.

### Mutability ve pür metotlar
Shallow yani yüzeysel karşılaştırma, iki nesnenin herbir key değerine karşılık gelen value değerlerini `===` operatörü ile karşılaştırır. Bu yüzden yukarıdaki mixin pür olarak isimlendirilmiştir çünkü mutable (değiştirilebilir) datayı sağlıklı bir şekilde karşılaştıramaz. Daha önce vermiş olduğumuz mutable array örneğini düşünelim:
 
```javascript
// temsili kod
this.setState({ data: [1, 2, 3] });

<MyComponent data={ this.state.data } />
```

`shallowCompare` metodu, bileşenin `props.data` değerini, bir sonraki güncellemede de yine aynı array olduğu için aynı olarak tanımlayacaktır. `data` isimli array'e eleman eklenmiş veya çıkarılmış olmasına bakmadan sadece `===` operatörü ile karşılaştırma yapacak ve aynı array olduğunu görecektir. Bu yüzden `props` değeri aynı olduğu için yeni bir render tetiklenmeyecektir. Eğer aynı arrayı, elemen ekleyip çıkardıktan sonra (mutate ettikten sonra) kullanmaya devam ediyorsak, yazdığımız ko pür olmaktan çıkmış demektir. 

[Redux](http://redux.js.org/) gibi sistemler bu yüzden pür fonksiyonlar kullanırlar. Eğer bir nesneyi (array olabilir) güncellemek istiyorsanız, öncelikle bunun bir kopyasını oluşturmalı ve güncellemeyi bu yeni nesne üzerinde yapmalısınız. Bu sayede  örneğin bir `state` nesnesinin içeriği değişiyor ise, kendisi (hafıza adresi) de değişeceği için, `===` operatörü ile kolayca aynı olup olmadıkları analaşılabilir. Ve yularıdaki gibi yüzeysel dahi olsa içerik karşılaştırmaya gerek kalmaz.

Diğer bir yöntem de, [Immutable.js](https://facebook.github.io/immutable-js/) gibi tamamen immutable (değiştirilemez) veri yapıları kullanmaktan geçer. Bu gibi kütüphaneler geliştiricileri değişkenleri yanlışlıkla mutate etmekten korurlar. Immutable veri yapıları kullanıldığında, nesneler için her değişik yapıldığında yeni bir nesne elde edersiniz. Yani bir array'e eleman eklediğinizde artık bambaşka bir array değeri (hafıza adresi) elde edersiniz. Bu saydede iki nesnenin değişip değişmediğini anlamak çok kolay bir hale gelir [^2].

### Render'i kaynağında önlemek

Bileşen ağaç yapımızı hatırlayacak olursak:
 
![](../birth/react-element-tree.png)

Eğer **A** bileşeninde bir güncelleme olursa, tüm alt bileşenler de güncelleme sürecine girer. Bu şekilde ciddi performans sorunları ortaya çıkabilir. Çünkü **A** bileşenindeki her bir küçük de olsa güncelleme için belki birçok bileşeni güncelleme evresine sokabiliriz. `shouldComponentUpdate` metodu ile güncellenme kontrolünü **A** bileşeninde yaparsak, tüm bu gereksiz güncellemeleri önlemiş oluruz. Bu da ciddi bir performans optimizasyonu demektir. Ancak şu da unutulmamalıdır ki, eğer **A** bileşeni gerekli `props` değerlerini alt bileşenlere aktarmıyor ise, kendisi de render olmadığı içi, alt bileşenlerden bazıları gerekli render'leri de kaçırabilir.

## `forceUpdate()`

`componentWillReceiveProps` metodu gibi, `shouldComponentUpdate`metodu da `forceUpdate` çalıştırılarak atlanılabilir. `forceUpdate` metodu çalıştırıldığında, React bileşen örneğini `dirty` kuyruğuna eklerken üzerine bir `flag` eklenir. Bu sayede `shouldComponentUpdate` metodu çalıştırılmaz.  
 
`forceUpdate` metodu zorlama bir metot olduğu için kullanırken çok dikkatli olmak gerekir. Sonsuz bir render döngüsü başlatmak olası tehlikeler içerisindedir. 

***Gelecek bölüm:*** [`componentWillUpdate()` metodu](tapping_into_componentwillupdate.md)

---

[^1] [React 15.0.1](https://github.com/facebook/react/blob/15-stable/src/addons/shallowCompare.js) kaynak kodundan alınmıştır

[^2] Eğer Immutable.js kullanırsanız, [ImmutableRenderMixin library](https://github.com/jurassix/react-immutable-render-mixin) adında bir mixin var. Bu mixin ile hem yüzeysel nesne kontrolü hem de immutable data kontrolü yapılabilmektedir. 

Daha önce bahsettiğimiz gibi, eğer kullandığınız veriyapısı immutable değil ise, değişip değişmediğini anlamak için içeriğini kontrol etmeniz gerekmektedir. Bu da yüzeysel (shallow) ya da derin (deep) karşılaştırmalar ile mümkün olur. Eğer nesne büyük ise, ciddi bir hesaplama maliyeti getirecektir. 

Eğer veriyapısı immutable ise, içeriğini kontrol emeniz gerekmez. Her bir içerik değişiminde zaten değişken yeni bir değişkene kopyalanır ve farklı bir hafıza adresine işaret eder. Böylece javascript'in mutlak eşitlik `===` operatörüyle kolayca karşılaştırılabilirler. 
