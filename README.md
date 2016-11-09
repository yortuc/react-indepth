# React ile arayüz geliştime 

Büyük ölçüde [React-in-depth](https://github.com/DevelopmentArc/react-indepth) GitBook'undan çeviri olmakla birlikte, kendi notlarımı ve örneklerimi de derli toplu bir şekilde tutabilmek amacıyla bu dökümanı oluşturdum.

Amaç, React ile arayüz geliştme sürecinin detaylı olarak incelenmesidir. React felsefesi temelde iki direk üzerine kuruludur.  

1. Uygulamanın birbirinden bağımsız, tekrar kullanılabilir bileşenler tarafından oluşturulması. (Bunun için de her bir bileşenin kendi yaşam döngüsünü [component lifecycle] incelemiz gerekiyor.)

2. İki veya daha çok bileşenin birlikte kullanılarak daha karmaşık bileşenler oluşturulması (compositing).

Bu dökümanda da bu yol izlenecek ve öncelikle bir React bileşeninin yaşamı  takip edildikten sonra, bileşenlerin bir biri içerisinde kullanımı ile uygulama geliştirilecektir. 