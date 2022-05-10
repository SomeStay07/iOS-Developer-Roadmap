# Принципы SOLID

Это не паттерны, их нельзя назвать догмами, которые надо обязательно применять при разработке, однако их использование улучшает код программы, упрощает его изменения и поддержку.
Т.е в идеале - придерживаться всех 5 акронимов, но исходя из ситуации, некоторыми, как например `D` или `L` - можно принебрегать, т.к по-хорошему `S` и `O` всегда нужно придерживаться (мнение автора этого репозитория).
#

[`Single Responsibility Principe`](https://medium.com/movile-tech/single-responsibility-principle-in-swift-61ee11ee81b5) - Каждый объект должен иметь одну ответственность и эта ответственность должна быть полностью инкапсулирована в эту сущность. 
Всё его поведение должно быть направлены исключительно на обеспечение этой ответственности.

Пример: есть проект, неважно с какой архитектурой, и вот акроним `S` означает, что у вас есть файл, где вы только отрисовываете `UI` интерфейс, так вот этот файл и должен только отрисовывать, т.е запрос в сеть/бд/менджер файлов, реализованный в этой сущности
будет нарушать принцип акронима `S`, т.к уже кроме отрисовки, тут будет содержать бизнес логика, а это уже две ответственности.

Пример реализации `SRP` принципа, данный класс только отрисовывает `UI` элементы.
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

Но если добавить что-то иное, не связанное с отрисовкой, то это будет нарушеним `SRP` принципа, чтобы такого не было, нарушение принципа, единственным верным решением будет -
вынести сохранение в отдельную сущность(файл) и там реализовать сохранение.
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

[`Open Closed Principle`](https://medium.com/movile-tech/open-closed-principle-in-swift-6d666270953d) - Программные сущности (классы, модули, функции и т. п.) должны быть открыты для расширения, но закрыты для изменения, другими словами, вы расширяете его поведение, не изменяя его имплантацию.
Т.е:
  - **Открыто для расширения:** Вы должны иметь возможность расширить или изменить поведение без координальных/больших изменений.
  - **Закрыто для изменения:** Необходимо расширить класс без изменения реализации.

Пример: У нас есть класс **Logger**, который итерирует массив автомобилей и печатает детали автомобилей.
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
Если вы хотите добавить возможность печатать детали нового класса, то необходимо изменять реализацию `printData` каждый раз, когда мы хотим записать новый класс, который нарушает принцип открытого закрытия.

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
Мы можем решить эту проблему, создав новый протокол **Printable**, который будет реализован классами для регистрации. Тем самым **printData()** напечатает массив **Printable**.

Таким образом, создается новый абстрактный слой между **printData()** и классом для регистрации, позволяющий печатать другие классы, такие как **Bike**, и без изменения реализации **printData()**.

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
[`Liskov Substitution Principle`](https://medium.com/movile-tech/liskov-substitution-principle-96f15559e363) - Принцип подстановки Барбары Лисков. Объекты в программе должны быть заменяемыми на экземпляры их подтипов без изменения правильности выполнения программы. 
Наследующий класс должен дополнять, а не изменять базовый. Этот принцип может помочь вам использовать наследование, не нарушая его.

Пример: 
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
[Interface Segregation Principle](https://medium.com/movile-tech/interface-segregation-principle-in-swift-1778bab4452b) - Принцип разделения интерфейсов говорит о том, что слишком «толстые» интерфейсы необходимо разделять на более маленькие и специфические, чтобы программные сущности маленьких интерфейсов знали только о методах, которые необходимы им в работе.

Пример:

```
// Изначально у нас есть один протокол, который реализует функцию отрисовки

protocol DrawableProtocol {    
    func didDraw()
} 

// После, вы решили добавить еще несколько методов отрисовки

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

// Даже если вам потребуется отрисовать только квадрат, то всё - равно остальные функции будут подтянуты протоколом

class DrawSquare: DrawableProtocol { 
    func didDraw() { }
    
    func didDrawCircle() { }
    
    func didDrawSquare() { }
}
```

Исходя из принципа `ISP`, реализация кода выше должна быть такой:

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

Пример:
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
**FileSystemManager** - это низкоуровневый модуль, который легко использовать в других проектах. Проблема заключается в том, что модуль **High-level** **Handler** не используется повторно, поскольку он тесно связан с **FileSystemManager**. Мы должны иметь возможность повторно использовать модуль высокого уровня с различными типами хранилищ, такими как база данных, облако и т.д.

Мы можем решить эту зависимость с помощью протокола **Storage**. Таким образом, **Handler** может использовать этот абстрактный протокол без учета типа используемого хранилища. При таком подходе мы можем легко перейти от файловой системы к базе данных.

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
