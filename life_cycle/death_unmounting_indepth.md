# Bileşen ölümü

Bileşenimiz güncelleme aşamasında da hayatını sürdürdükten sonra, kaçınılmaz olarak ölüm sürecine girer. Ölüm sürecinde NativeUI katmanından sökülür (unmount) ve `Garbage Collection` için işaretlenir.  

Bileşenin ölüm sürecine girmesi, UI değişmesi ve artık bileşen ağacında bileşen örneğimize karşılık gelen bir `key` değeri kalmaması ile başlar. Bu işlem gerçekten ağaç yapısının değişmesi ile gerçekelebişeceği gibi, bileşen için programsal olarak `key` değerinin değiştirilmesiyle de olabilir (yeni bir bileşen örneği oluşumu için zorlama). Bir kere ölüm sürecine girildiğinde React bileşen ve alt bileşenlerle ilgili süreci başlatır.

## `componentWillUnmount()` metodu

Bu metot, bileşen NativeUI katmanından sökülmeden hemen önce çalışarak bize temizlik yapma imkanı verir. Genellikle `componentWillMount` metodu içerisinde yaptığımız yapılandırmanın tersini yaparak, kaynakları serbest bırakırız. 

Örneğin herhangi bir global olay dinleme işlemini sonlandırmak için (unsubscribe) ya da 3. parti bir kütüphanenin oluşturduğu elemanları yoketmek için en doğru yerdir. Eğer gerekli temizlik yapılaz ise uygulamada hafıza sızıntıları (memory leak) oluşabilir.
 
![](react-delete-tree.png)
 
React işlemlere, kalıdırlmak üzere olan bileşen için `componentWillUnmount` metodunu çalıştırarak başlar (örnekte **A.0**). Daha sonra ilk alt bileşenden başlanarak (**A.0.0**) sırası ile tüm alt bileşenler üzerinde (varsa onların da alt bileşenleri üzerinde) en alt seviyeye kadar `componentWillUnmount` her bir bileşen örneği için `componentWillUnmount` metodu çalıştırılır. Tüm `componentWillUnmount` metotları çalıştıktan sonra bileşen UI katmanından kaldırılır ve `Garbage Collector` için hazır hale gelir.
 
 ***Gelecek bölüm:*** [Yaşam döngüsü özet](the_life_cycle_recap.md) 
