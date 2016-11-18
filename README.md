# React İle Arayüz Geliştime 

Büyük ölçüde [React-in-depth](https://github.com/DevelopmentArc/react-indepth) GitBook'undan çeviri olmakla birlikte, kendi notlarımı ve örneklerimi de derli toplu bir şekilde tutabilmek amacıyla bu dökümanı oluşturdum.

Amaç, React ile arayüz geliştme sürecinin detaylı olarak incelenmesidir. React felsefesi temelde iki direk üzerine kuruludur.  

1. Uygulamanın birbirinden bağımsız, tekrar kullanılabilir bileşenler tarafından oluşturulması. (Bunun için de her bir bileşenin kendi yaşam döngüsünü [component lifecycle] incelemiz gerekiyor.)

2. İki veya daha çok bileşenin birlikte kullanılarak daha karmaşık bileşenler oluşturulması (compositing).

# İçindekiler

* [Giriş](react_basics/introduction.md)
* [React Yaşam Döngüsü](life_cycle/introduction.md)
   * [Yaşam Döngüsü Metodları](life_cycle/lifecycle_methods_overview.md)
   * [Bileşen Doğum Süreci](life_cycle/birth_mounting_indepth.md)
       * [Bileşen doğumu](life_cycle/birth/initialization_and_construction.md)
       * [componentWillMount() ile Mount öncesi](life_cycle/birth/premounting_with_componentwillmount.md)
       * [Bileşen render() metodu](life_cycle/birth/component_render.md)
       * [Mount kavramı ve çocuk bileşenler](life_cycle/birth/managing_children_components_and_mounting.md)
       * [componentDidMount() ile mount sonrası](life_cycle/birth/post_mount_with_component_did_mount.md)
   * [Bileşen gelişim dönemi](life_cycle/growth_update_indepth.md)
       * [Güncelleme ve `componentWillReceiveProps()` metodu](life_cycle/update/component_will_receive_props.md)
       * [shouldComponentUpdate() kullanımı](life_cycle/update/using_should_component_update.md)
       * [componentWillUpdate() metodu](life_cycle/update/tapping_into_componentwillupdate.md)
       * [Yeniden render etme ve çocuk bileşen güncellemeleri](life_cycle/update/rerendering_and_children_updates.md)
       * [componentDidUpdate() ile render sonrası](life_cycle/update/postrender_with_componentdidupdate.md)
   * [Bileşen Ölümü](life_cycle/death_unmounting_indepth.md)
   * [Yaşam Döngüsü Özet](life_cycle/the_life_cycle_recap.md)
* [Bileşen evrimi ve kompozisyon](patterns/component_evolution_and_composition.md)
   * [Bir Liste bileşeninin evrimi](patterns/the_evolution_of_a_list_component.md)
   * [Farklı içerik render etme](patterns/rendering_different_content.md)
   * [Yüksek Dereceli Bileşenler](patterns/higher_order_components.md)
* [About the Authors](about_the_authors.md)