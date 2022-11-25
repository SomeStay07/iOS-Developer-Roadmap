# Принципы SOLID

Это не паттерны и их нельзя назвать догмами, которые обязательно применять при разработке, однако следование этим принципам улучшает код программы, упрощает его изменение и поддержку.

В первую очередь, всегда старайтесь придерживаться `S` и `O`. Что же касается `D` и `L`, порой ими можно пренебречь, если того требует контекст. 
В идеале, разумеется, следует придерживаться всех 5 акронимов (мнение автора этого репозитория).
#

[`Single Responsibility Principe`](https://medium.com/movile-tech/single-responsibility-principle-in-swift-61ee11ee81b5) - Каждый объект должен иметь одну ответственность и эта ответственность должна быть полностью инкапсулирована в эту сущность. 
Всё поведение объекта должно быть направлено исключительно на обеспечение этой ответственности.

К примеру, следование первому принципу(`S`) может означать, что в вашем файле с отрисовкой `UI` не должно быть иного функционала. Добавление в этот файл бизнес-логики (e.g., запроса в базу данных) или чего угодно, отличного от отрисовки интерфейса, также добавляет еще одну ответственность, и тем самым нарушает принцип акронима `S`.

Пример реализации `S`-принципа, данный класс выполняет единственную задачу - отрисовывает элементы интерфейса.
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

Но вы всё еще можете добавлять функционал, не нарушая принцип `S`. 
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
#

[`Open Closed Principle`](https://medium.com/movile-tech/open-closed-principle-in-swift-6d666270953d) - Программные сущности, такие как классы, модули, функции, должны быть открыты для расширения, но закрыты для изменения. Другими словами, вы расширяете их поведение, не изменяя имплантацию.

  - **Открыто для расширения:** Расширение или изменение поведения без кардинальных трансформаций.
  - **Закрыто для изменения:** Расширение класса без изменения реализации.

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
#
[`Liskov Substitution Principle`](https://medium.com/movile-tech/liskov-substitution-principle-96f15559e363) - Принцип подстановки Барбары Лисков. Объекты могут заменяться на экземпляры их подтипов, сохраняя работоспособность программы. 
Класс-наследник должен дополнять базовый класс, а не изменять его. Этот принцип может помочь использовать наследование корректно.
 
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
#
[Interface Segregation Principle](https://medium.com/movile-tech/interface-segregation-principle-in-swift-1778bab4452b) - Принцип разделения интерфейсов говорит о том, что слишком «толстые» интерфейсы необходимо разделять на меньшие и специфические, чтобы программные сущности меньших интерфейсов знали только о методах, которые необходимы им в работе.


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

Исходя из принципа `I`, реализация кода должна быть следующей:

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
#
[Dependency Inversion Principle](https://medium.com/movile-tech/dependency-inversion-principle-in-swift-18ef482284f5) - Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций. 
Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.

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
