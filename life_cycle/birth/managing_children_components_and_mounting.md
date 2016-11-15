# Çocuk bileşenlerin yönetimi ve Mounting

İlk `render()` metodunu geçtik ve çıktı olarak tek bir React öğesi (`ReactElement`) elde ettik. Bu öğe, tersine bir ağaç yapısı gibi alt öğeler de barıdırabilir. Ve bu öğeler de kendi altında öğeler barındırabilir.

![React öğesi ağacı](react-element-tree.png)

Bu şekildeki ağaç şeklinde bir React öğesi yapısı için de, her bir öğe kendi yaşam döngüsünü geçirmek zorundadır. Üst öğe gibi, her alt öğe için de React birer bileşen örneği (instance) oluşturur. Onlar da yapılandırma, `props` ve `state` ilk değerlerinin atanması, `componentWillMount()` ve `render()` aşamalarınandan geçer. Eğer çocuk bileşen de çocuklara sahip ise, en alt seviyeye inilene kadar bu işlem devam eder. 

React'ın en güçlü yanlarından birisi de bu şekilde "nested" (iç içe geçmiş) yapılar ile kolayca karmaşık arayüzlerin oluşturulabilmesidir. Bileşenlerin mümkün olduğunca basit tutulması, buna karşılık bu basit bileşenlerin birlikte etkili kullanımı ile (composing) arayüzlerin oluşturulması tavsiye edilmektedir. [^1]

***Gelecek Bölüm:*** [Mount sonrası ve `componentDidMount()` metodu](post_mount_with_component_did_mount.md)

---

[^1] See [Presentational and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.pnmirdrso) by Dan Abramov for more details
