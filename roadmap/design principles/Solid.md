# Принципы SOLID

Это не паттерны и их нельзя назвать догмами, которые обязательно применять при разработке, однако следование этим принципам улучшает код программы, упрощает его изменение и поддержку.

В первую очередь, всегда старайтесь придерживаться `SRP` и `OCP`. Что же касается `DIP` и `LSP`, порой ими можно пренебречь, если того требует контекст. 
В идеале, разумеется, следует придерживаться всех 5 акронимов (мнение автора этого репозитория).

# Single Responsibility Principe
[`Single Responsibility Principe`](https://solidbook.vercel.app/srp) - Каждый объект должен иметь одну ответственность и эта ответственность должна быть полностью инкапсулирована в эту сущность. 
Всё поведение объекта должно быть направлено исключительно на обеспечение этой ответственности.

К примеру, следование первому принципу (`SRP`) может означать, что в вашем файле с отрисовкой `UI` не должно быть иного функционала. Добавление в этот файл бизнес-логики (запрос в базу данных, запрос на сервер) или всего того, что отличается от отрисовки интерфейса и также добавляет еще одну ответственность, и тем самым нарушает принцип акронима `SRP`.

Т.е принцип `единственной ответственности`:
- Помогает разбивать и декомпозировать задачи по одной на модуль;
- Уменьшает количество модулей, которые надо изменить при изменении требований;
- Ограничивает влияние изменений, помогая контролировать сложность системы.

Пример реализации `SRP`- принципа, данный класс выполняет единственную задачу - отрисовывает элементы интерфейса.
```
class CustomView: UIView {

  private let view = UIView()
  
  private let label = CustomLabel()
  
  init() {
    setupAppearance()
  }
  
  func setupAppearance() {
    view.backgroundColor = .green
  }
  
  func set(data: SomeData) {
    label.set(with: data)
  }

}
```

Но вы всё еще можете добавлять функционал, не нарушая принцип `SRP`. 
Для этого реализуйте сохранение в отдельной сущности или файле.
```
class CustomView: UIView {

  private let view = UIView()
  
  private let label = CustomLabel()
  
  private let dataBase = SomeDataBase()
  
  init() {
    setupAppearance()
  }
  
  func setupAppearance() {
    view.backgroundColor = .green
  }
  
  func set(data: SomeData) {
    label.set(with: data)
  }

  func saveToDB(name: String) {
    dataBase.save(name)
  }

}
```

# Open Closed Principle
[`Open Closed Principle`](https://solidbook.vercel.app/ocp) - Помогает исключить такую проблему. Согласно ему модули должны быть открыты для расширения, но закрыты для изменения.

- **Открыто для расширения:** Расширение или изменение поведения без кардинальных трансформаций
- **Закрыто для изменения:** Расширение класса без изменения реализации.

Основная причина, по которой вносить изменения бывает трудно или дорого — когда небольшое изменение в одной части системы вызывает лавину изменений в других частях. Грубо и утрировано: если в программе для изменения цвета кнопки надо поправить 15 модулей, такая система спроектирована плохо.

Простыми словами — модули надо проектировать так, чтобы их требовалось менять как можно реже, а расширять функциональность можно было с помощью создания новых сущностей и композиции их со старыми.

Модули, которые `удовлетворяют OCP`:

открыты для расширения — их функциональность может быть дополнена с помощью других модулей, если изменятся требования;
закрыты для изменения — расширение функциональности модуля не должно приводить к изменениям в модулях, которые его используют.

Класс **Logger** итерирует массив автомобилей и печатает детали.
```
class Car {
    let name: String
    let color: String
    init(name: String, color: String) {
        self.name = name
        self.color = color
    }
   func printDetails() -> String {
        return "I have \(self.color) color \(self.name)."
    }
}

class Logger {
    func printData() {
        let cars = [ Car(name: "BMW", color: "Red"),
                     Car(name: "Audi", color: "Black")]
         cars.forEach { car in
             print(car.printDetails())
         }
     }
}
```
Но если вы хотите, чтобы класс **Logger** печатал детали других классов, потребуется вручную добавлять каждый новый класс в реализацию `printData`.

```
class Bike {
    let name: String
    let color: String
    init(name: String, color: String) {
        self.name = name
        self.color = color
    }
    func printDetails() -> String {
        return "I have \(self.name) bike of color \(self.color)."
    }
}

class Logger {
    func printData() {
        let cars = [ Car(name: "BMW", color: "Red"),
                     Car(name: "Audi", color: "Black")]
         cars.forEach { car in
             print(car.printDetails())
         }
        let bikes = [ Bike(name: "Homda CBR", color: "Black"),
                      Bike(name: "Triumph", color: "White")]
        bikes.forEach { bike in
             print(bike.printDetails())
         }
     }
}
```
Можно решить эту проблему, создав протокол **Printable**, который будет реализован классами для регистрации. Тем самым **printData()** напечатает массив **Printable**.

Таким образом, создается новый абстрактный слой между **printData()** и классом для регистрации, позволяющий печатать другие классы, такие как **Bike**, без изменения реализации **printData()**.

```
protocol Printable {
    func printDetails() -> String
}

class Car: Printable { 
    let name: String 
    let color: String
    init(name: String, color: String) {
        self.name = name
        self.color = color
    }
    func printDetails() -> String {
        return "I have \(self.color) color \(self.name)."
    }
}

class Bike: Printable {
    let name: String
    let color: String
    init(name: String, color: String) {
        self.name = name
        self.color = color
    }
    func printDetails() -> String {
        return "I have \(self.name) bike of color \(self.color)."
    }
}

class Logger {
    func printData() {
        let vehicles: [Printable] = [Car(name: "BMW", color: "Red"),
                                  Car(name: "Audi", color: "Black"),
                            Bike(name: "Honda CBR", color: "Black"),
                              Bike(name: "Triumph", color: "White")]
        vehicles.forEach { vehicle in
            print(vehicle.printDetails())
        }
    }
}
```

# Liskov Substitution Principle
[`Liskov Substitution Principle`](https://solidbook.vercel.app/lsp) - Принцип подстановки Барбары Лисков. Объекты могут заменяться на экземпляры их подтипов, сохраняя работоспособность программы. 
Класс-наследник должен дополнять базовый класс, а не изменять его. Этот принцип может помочь использовать наследование корректно.
Простыми словами — классы-наследники не должны противоречить базовому классу. Например, они не могут предоставлять интерфейс ýже базового. Поведение наследников должно быть ожидаемым для функций, которые используют базовый класс.

`Принцип подстановки Барбары Лисков:`

- Помогает проектировать систему, опираясь на поведение модулей;
- Вводит ограничения и правила наследования объектов, чтобы их потомки не противоречили базовому поведению;
- Делает поведение модулей последовательным и предсказуемым;
- Помогает избегать дублирования, выделять общую для нескольких модулей функциональность в общий интерфейс;
- Позволяет выявлять при проектировании проблемные абстракции и скрытые связи между сущностями.
 
```
let requestKey: String = "NSURLRequestKey"

// NSError subclass provide additional functionality but don't mess with original class.

class RequestError: NSError {
    var request: NSURLRequest? {
        return self.userInfo[requestKey] as? NSURLRequest
    }
}

// I forcefully fail to fetch data and will return RequestError.

func fetchData(request: NSURLRequest) -> (data: NSData?, error: RequestError?) {
    let userInfo: [String:Any] = [requestKey : request]
    return (nil, RequestError(domain:"DOMAIN", code:0, userInfo: userInfo))
}

func willReturnObjectOrError() -> (object: AnyObject?, error: NSError?) {

    let request = NSURLRequest()
    let result = fetchData(request: request)

    return (result.data, result.error)
}

let result = willReturnObjectOrError()

// RequestError

if let requestError = result.error as? RequestError {
    requestError.request
}
```

# Interface Segregation Principle
[Interface Segregation Principle](https://solidbook.vercel.app/isp) - Принцип разделения интерфейсов говорит о том, что слишком «толстые» интерфейсы необходимо разделять на меньшие и специфические, чтобы программные сущности меньших интерфейсов знали только о методах, которые необходимы им в работе.

`Принцип разделения интерфейса:`

- Помогает бороться с наследованием или реализацией ненужной функциональности;
- Даёт возможность спроектировать модули так, чтобы их затрагивали изменения только тех интерфейсов, которые они действительно реализуют;
- Снижает зацепление модулей;
- Уничтожает наследование ради наследования, поощряет использование композиции;
- Позволяет выявлять более высокие абстракции и находить неочевидные связи между сущностями.

```
// Изначально есть один протокол, реализующий функцию отрисовки

protocol DrawableProtocol {    
    func didDraw()
} 

// Далее, вы решили добавить еще несколько методов отрисовки

protocol DrawableProtocol {    
    func didDraw()    
    func didDrawCircle()    
    func didDrawSquare()
}
```
Если класс **CustomDraw** реализует протокол `DrawableProtocol`, то необходимо будет реализовать и все его методы.

```
class CustomDraw: DrawableProtocol { 
    func didDraw() { }
    
    func didDrawCircle() { }
    
    func didDrawSquare() { }
}

// Даже если вам потребуется отрисовать только квадрат, протокол всё равно подтянет остальные функции 

class DrawSquare: DrawableProtocol { 
    func didDraw() { }
    
    func didDrawCircle() { }
    
    func didDrawSquare() { }
}
```

Исходя из принципа `ISP`, реализация кода должна быть следующей:

```
protocol DrawableProtocol { 
    func didDraw()
}

protocol CircleDrawableProtocol {
    func didDrawCircle()
}

protocol SquareDrawableProtocol {
    func didDrawSquare()
}

class CustomDraw: DrawableProtocol, CircleDrawableProtocol, SquareDrawableProtocol { 
    func didDraw() { }
    
    func didDrawCircle() { }
    
    func didDrawSquare() { }
}

class DrawSquare: SquareDrawableProtocol { 
    func didDrawSquare() { }
}
```

# Dependency Inversion Principle
[Dependency Inversion Principle](https://medium.com/movile-tech/dependency-inversion-principle-in-swift-18ef482284f5) - Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций. 
Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.

`Зацепление и связность`

Зацепление (coupling) не стоит путать со связностью (cohesion).

[Зацепление](https://ru.wikipedia.org/wiki/Зацепление_(программирование)) — степень взаимозависимости разных модулей. Чем выше зацепление, тем более хрупкой получается система, и тем сложнее вносить изменения.

[Связность](https://ru.wikipedia.org/wiki/Связность_(программирование)) — степень, в которой задачи некоторого модуля, связаны друг с другом. Чем выше связность, тем строже модули следуют SRP, тем выше сфокусирован модуль на конкретной задаче.

`DIP и тестируемость`
При тестировании модуля, который зависит от других модулей, нам нужно либо создавать экземпляр каждой зависимости, либо создать заглушки.

DIP упрощает тестирование системы. Если модули зависят от интерфейсов, нам достаточно создать заглушку, реализующую этот интерфейс.

`Принцип инверсии зависимостей:`

- Вводит правила и ограничения для зависимости одних модулей от других;
- Снижает зацепление модулей;
- Делает тестирование модулей проще;
- Позволяет проектировать систему так, чтобы модули были заменяемы на другие.

```
class FileSystemManager {
    func save(string: String) {
        // Открыть файл
        // Сохранить string в этот файл
        // Закрыть файл
   }
}

class Handler {
    let fileManager = FilesystemManager()
    func handle(string: String) {
        fileManager.save(string: string)
    }
}
```
**FileSystemManager** - это низкоуровневый модуль, который легко использовать в других проектах. Проблема заключается в том, что модуль **High-level** **Handler** не используется повторно, поскольку он тесно связан с **FileSystemManager**. Вы должны иметь возможность повторно использовать модуль высокого уровня с различными типами хранилищ (e.g., база данных, облако).

Можно решить эту зависимость с помощью протокола **Storage**. Таким образом, **Handler** может использовать этот абстрактный протокол без учета типа хранилища. При таком подходе легко перейти от файловой системы к базе данных.

```
protocol Storage {
    func save(string: String)
}

class FileSystemManager: Storage {
    func save(string: String) {
        // Открыть файл
        // Сохранить string в этот файл
        // Закрыть файл
    }
}

class DatabaseManager: Storage {
    func save(string: String) {
        // Сконнектить базу данных
        // Выполнить запрос для сохранения строки в таблице
        // Завершить соединение с базой данных
    }
}

class Handler {

    let storage: Storage

    init(storage: Storage) {
        self.storage = storage
    }
    
    func handle(string: String) {
        storage.save(string: string)
    }
}
```

# Дополнительный материал:
- [Single Responsibility Principle. Не такой простой, как кажется](https://habr.com/ru/post/454290/)
- [Refactoring and Open / Closed principle](https://softwareengineering.stackexchange.com/questions/170547/refactoring-and-open-closed-principle)
- [Ковариантность и контравариантность](https://ru.wikipedia.org/wiki/Ковариантность_и_контравариантность_(программирование))
