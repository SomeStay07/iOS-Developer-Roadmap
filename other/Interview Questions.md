# Вопросы для разогрева.
- Рассказать про принципы SOLID, YAGNI, KISS. [Вот можно посмотреть](https://www.youtube.com/watch?v=TxZwqVTaCmA) так же [последняя глава](https://www.chitai-gorod.ru/catalog/book/830585/) в этой книги отлично раскрывает эти принципы.
- Рассказать про принципы [ООП](https://www.youtube.com/watch?v=-6DWwR_R4Xk): наследование, инкапсуляция, полиморфизм и абстракция.
- В чем разница между композицией и наследованием? Хорошая статья для изучения [тут](https://habr.com/ru/post/325478/), [пример с кодом тут](https://www.avanderlee.com/swift/composition-inheritance-code-architecture/) и еще вот [тут.](https://betterprogramming.pub/swift-favor-composition-over-inheritance-the-baseviewcontroller-case-f598064bda6)
- Разница между `mock` и `stub` для тестирования, изучить можно [вот тут](https://stackoverflow.com/questions/3459287/whats-the-difference-between-a-mock-stub).

# Устройство памяти.
- Рассказать про виды памяти.
  - Разница между `heap` и `stack`. 
  - Разница между `value` и `reference` типами. 
  - Когда использовать `класс` для работы? Когда использовать `структуру` для работы? 
  - Как положить `value` типы в хип?
  - Как положить `reference` типы на стек?
  - Что такое `Экзистенциальный контейнер`?
  - Что такое `Inout аргумент`?
  - [Вот здесь](https://github.com/SomeStay07/iOS-Developer-Roadmap/blob/main/roadmap/memory%20management/Data%20type.md) можно про всё, что выше, почитать.
  - Что такое [Copy on Write](https://github.com/SomeStay07/iOS-Developer-Roadmap/blob/main/roadmap/memory%20management/Copy%20on%20write.md)?
  - Что такое [`indirect enum`](https://stackoverflow.com/a/37533442)?
  - Разница между [`== vs ===`](https://stackoverflow.com/a/50645846)?

# ARC
- Что такое [`ARC`](https://youtu.be/SnvZwVgoUrI?t=159)?
- Что такое [`MRC`](https://youtu.be/SnvZwVgoUrI?t=25)?
- Разница между `ARC и MRC`?
- Разница между `ARC и GC`?
- Что такое [`retain cycle`](https://youtu.be/SnvZwVgoUrI?t=225)?
- На каком этапе [`расставляются ссылки` в ARC](https://youtu.be/SnvZwVgoUrI?t=174)?
- Разница между [`weak` и `unowned`]()?
- Что такое [`side table`](https://github.com/SomeStay07/iOS-Developer-Roadmap/blob/main/roadmap/memory%20management/ARC/Side%20table%20and%20object%20reletionship.md#side-table)?
- Как устроена и когда появляется [`side table`](https://github.com/SomeStay07/iOS-Developer-Roadmap/blob/main/roadmap/memory%20management/ARC/Side%20table%20and%20object%20reletionship.md#как-устроена-side-table)?
- Счетчик ссылок и жизненный цикл объекта, [ссылка](https://github.com/SomeStay07/iOS-Developer-Roadmap/blob/main/roadmap/memory%20management/ARC/Side%20table%20and%20object%20reletionship.md#счетчик-ссылок)
- Что такое [`autoreleasepool`](https://swiftrocks.com/autoreleasepool-in-swift)?
- Почему не будет `retain cycle` для `UIView.animate`, `DispatchQueue.main` и т.д? [Ответ](https://stackoverflow.com/a/48420485)

# Многопоточность
- В чем разница sync и async?
- [Потокобезопасность со static](https://stackoverflow.com/a/58038780)

# Others
- Что такое `generics` и для чего они нужны?
- Отличие [`отличие static методов от class`](https://habr.com/ru/sandbox/146984/) функций.
- Что такое [`responder chain`](https://habr.com/ru/post/464463/)?
