# React Yaşam Döngüsü
Yaşayan bir organizmayı tanımlamanın en iyi yolu yaşam döngüsünü incelemektir. Genel olarak doğma, gelişme ve kaçınılmaz olarak ölüm şeklinde sıralanabilir. Arayüz (UI) uygulamaları da genellikle aynı yoldan geçerler. Uygulama başladığında bu bir "doğum" olarak değerlendirilebilir. Kullanıcının uygulama ile etkileşime geçmesi "büyüme" ve son olarak da kullanıcının uygulamayı kapatması da "ölüm" olarak görülebilir. 

Uygulamayı oluşturan bileşenler de aynı yolu izler. React dünyasında uygulama Bileşen'lerden (Component) oluşur. Bileşenlerin doğması, yaşaması ve ölmesi, uygulama genel yaşam döngüsü içerisinde çokca görülür. Bileşen yaşam döngüsü sürecinin anlaşılması ile daha hızlı ve tutarlı geliştirme yapılabilmesinin yanı sıra optimizasyon ve genel uygulama sağlığı da iyileştirilmiş olur.

## React bileşenlerinin yaşam döngüsü evreleri

Tüm UI sistemleri bileşen tabanlı bir mimari kullanmaz. Bu yapı bir sistemin daha "iyi" ya da "kötü" olduğunu göstermez. Yaşam döngüsü dediğimiz süreç aslında bir dizi olayların belirli bir sıra takip edilerek sistem tarafından ateşlenmesidir. React yaşam döngüsü doğum, büyüme ve ölme şeklinde devam etmektedir. React ekibi bu sürece dahil olabilimemiz için biz geliştiricilere gerekli mekanizmayı sağlamaktadır. Bu sürece dahil olabilmek için gerekli metodları oluşturup ya da override etmemiz gerekmektedir. 

### [Aşama 1: Doğma](birth_mounting_indepth.md)
 
Bir React bileşenin ilk aşaması doğum (Mounting: DOM veya rendering contex'i üzerine oturma) olarak tanımlanır. Biz de bileşenle ilgili ilk yapılandırma işlemlerini burada yapıyoruz. Bu aşamada bileşenin state ve props değerleri tanımlanmış ve ataması yapılmıştır. Bileşen, ve bileşenin tüm çocukları hangi context'e render ediliyo ise (DOM veya UIView ...) oturtulmuştur. Biz de bu oturma üzerine, gerekli post-process işlemlerini yapabiliriz. Bu olay, bileşenin yaşamı boyunca sadece bir kere tetiklenmektir. 

### [Aşama 2: Büyüme / Gelişme](growth_update_indepth.md)

Diğer aşama da büyüme ve gelişme aşamasıdır. Bu aşamada bileşen, çeşitli güncellemelere maruz kalır. Bileşen state'i değişir, yeni props değerleri ile güncellenir ve kullanıcı ile etkileşime girer. Bileşenin yaşamının büyük kısmı bu aşamada geçer. Doğum ve ölümün aksine bu aşama sürekli tekrarlanır.

### [Aşama 3: Ölüm](death_unmounting_indepth.md)

Bileşenin yaşamındaki son aşamadır. Bileşen rendering contex'inden kaldırıldığı anda gerçekleşir. Kullanıcı uygulamayı kapattığınıda, araryüz değişip bazı bileşenler kaldırıldığında gerçekleşebilir.Ölüm sadece bir kez gerçekleşir ve bileşen Garbage Collection için hazır hale getirilerek hafızadan silinir.

***Gelecek Bölüm:*** [Yaşam döngüsü aşamaları](lifecycle_methods_overview.md)
