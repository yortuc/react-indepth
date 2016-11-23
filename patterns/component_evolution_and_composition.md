# Bileşen evrimi ve kompozisyon

Bileşenlerin tekrar kullanılabilmesi (reusability) ve birlikte iç içe kullanılabilmesi (composability) React'in en temel ilkeleridir. Bu iki ilke sayesinde, uygulama karmaşıklaştıkça geliştirme zamanı ciddi şekilde düşer. Ancak tekrar kullanılabilir bileşen geliştirebilmek; planlama ve birçok kullanım alanını düşünebilmeyi gerektirir.

Bileşenin amacının ne olacağının kesin hatlarının çizilmesi, tekrar kullanılabilir bileşen geliştirmedeki ilk adımdır. Bazen bir bileşenin çok farklı şekillerde kullanılacağını önceden planlarız. Bu gibi durumlarda planımızı buna göre yaparız. Diğer bir durumda ise bileşenin amacı uygulama geliştikçe değişir. Bir bileşenin nasıl evrilmesi gerekitğini bilmek de, tekrar kullanılabilirliği yakalamak kadar önemlidir.

##Uygulamayı tasarlamak

Çoğu zaman abartı şekilde tasarlanmış (over-architected) sistemler görürüz. Uygulama yaşamı boyunca geçebilecek her senaryoyu baştan planlamaya çalışmaktan kaynaklanmaktadır. Bu yaklaşım, sisteme gereksiz karmaşıklık getirdiği için çok verimsizdir.

Diğer bir taraftan da hiç bir esneklik vaadetmeyen uygulamalar geliştirmek de istemeyiz. İlk etapta geleceği hiç düşünmeden geliştirme yapmak kolaylık gibi gözükse de yeni bir özellik eklenmesi gerektiği durumda problemler ortaya çıkar. 

Bu iki uç durum arasındaki dengeyi kurmak, uygulama mimarisi açısından en zor kısımdır. Gerektiğinde kolayca genişletilebilecek esnek bir uygulama istiyoruz ama olabilecek her ihtimali planlayarak vakit kaybetmek de istemiyoruz.

Uygulama mimarisi açısından diğer bir zorluk da, ihtiyaçları anlayabilmektir. Geliştirici olarak, bazen problemi tam olarak anlayabilmek için, çözümü tam olarak geliştirmemiz gerekir. Bu da göstermektedir ki uygulama mimarisi kurgulamak canlı bir süreçtir. Uygulama ilerledikçe, problem ile ilgili fikirlerimiz artacak ve de bu uygulama mimarisi kurgusuna etki ederek değişmesine, evrilmesine yol açacaktır. 
Bu açıdan bileşenlerin `refactor` edilerek tekrar gözden geçirilmesi uygulamanın başarıyla tamamlanabilmesi için hayati önem taşır. 

Bir bileşenin zaman içerisinde nasıl evrilebileceğini bir örnek ile inceleyeceğiz. Öncelikle çok basit bir yaklaşımla bir Liste bileşeni oluşturmaya başlayacağız. Daha sonra farklı `refactor` aşamalarından geçirerek, bileşenin tekrar kullanılabilirliğini artıracağız. 

***Gelecek bölüm***: [Bir liste bileşeninin evrimi](the_evolution_of_a_list_component.md)
