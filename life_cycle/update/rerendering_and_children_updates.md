# Yeniden render etme ve alt bileşen güncellemeleri

Tekrar meşhur render metoduna geri döndük. Artık `props` ve `state` değerleri güncellendi [^1] ve bunları bileşen çıktısına ve alt bileşenlere yasıtabiliriz. Geliştirici açısıdan gerçekleşmek üzere olan bu render için tüm [ `ilk render` ](../birth/component_render.md) kuralları geçerlidir.

Ancak ilk renderden farklı olarak, React'in bu render sürecini farklı yönetir. Bu farklılık bileşenlerin yapılandırılması (initialization) ve alt bileşenlerin yönetilmesinde görülür.

![](react-tree-update.png)

React, mevcut sanal dom ağaç yapısını (ReactElement 'lerden oluşan) ve render metoduyla oluşturulan yeni sanal dom ağaç yapısını karşılaştırır. Yeni bir ReactElement yaratılıp yaratılmadığını (**A.3**), kaldırılan öğeler olup olmadığını (**A.0.1**) veya mevcut olanlardan güncellemesi gerektiğini (**A**, **A.0**, **A.0.0**) hesaplar. 

Eğer öğelerin `key` değerleri aynı ise, React yeni `props` değerlerini öğeye aktararak, öğeye karşılık gelen bileşen örneğini (Component instance) güncelleme evresine sokar. Eğer yeni öğeler eklenmil ya da mevcut olanların `key` değeri değişmiş ise React, öğe'ye bakarak bunlara karşılık gelen bileşen örneklerini oluşturur. Bu yeni oluşturulan bileşenler de Doğum sürecine sokulur. 


 ***Gelecek bölüm:*** [`componentDidUpdate()` metodu ve render sonrası](postrender_with_componentdidupdate.md)
 
 ---
 
 [^1] Daha `state` değerinin güncellenme işleminin asenkron olduğunu ve `setState` ile bir güncelleme yapıldıktan hemen sonra aynı senkron metot zinciri içerisinde `state` değerinin güncelleneceğinin garantisi olmadığından bahsetmiştir. Güncelleme sürci son olarak `render` metoduna geldiğinde bu güncellemenin yapılmış olduğu garanti edilir. Sonuç olarak render metodu içerisinde `state` ve `props` değerleri en güncel hali ile ulaşılabilir durumdadır. 