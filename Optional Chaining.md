# Unwrapping Optionals With Optional Binding in Swift
**Optional binding** - это встроенный в swift механизм безопасного извлечения значений. 

  - При помощи оператора **if** мы можем развернуть опциональный тип, сделать его константной или переменной.
```
struct Car {
    var name: String?
    
    func run() {
        print("Poehali")
    }
}

var possibleStructCar: Car? = Car()

if let car = possibleStructCar {
    car.run()
}
```
Т.к мы использовали `let`, то этот код не сможет поменять своё значение:
```
if let car = possibleStructCar {
    car.name = "BMW"
}
// Error: Cannot assign to property: 'car' is a 'let' constant
```
Это происходит потому-что **Car** является структурой, которая является типом значения, и значения в Swift являются неизменяемыми. 
Единственный способ изменить значение - создать новое значение и заменить существующее на новое. 
В приведенном выше случае необходимо присвоить константе **car** новое значение, что невозможно. Поэтому мы получаем ошибку. Это не было бы проблемой, если бы мы имели дело с экземпляром класса, поскольку классы являются ссылочными типами. Переменная, которой назначен экземпляр класса, содержит только ссылку на экземпляр класса, выделенный в куче, рассмотрим это, определив `CarClass`.

```
class CarClass {
    var name: String?
    
    init(name: String? = nil) {
        self.name = name
    }
}
```

Теперь наш `CarClass` объект стал ссылочным типом, тем самым, задекларировав его как константу мы сможем изменять его параметры:
```
var possibleCarClass: CarClass? = CarClass()

if let carClass = possibleCarClass {
    carClass.name = "BMW"
}
```

Код выше работает потому-что мы не мутировали ссылку, удерживаемую константой **carClass**, а просто использовали ее для мутации экземпляра **CarClass**. 
Мы получили бы ошибку, если бы попытались дать константе **carClass** ссылку на новый экземпляр **CarClass**, как это показано ниже.

```
if let carClass = possibleCarClass {
    carClass = CarClass()
}
// Error: Cannot assign to value: 'carClass' is a 'let' constant
```

Для ситуаций, когда необходимо использовать **optional binding** для извлечения обернутого значения в переменную, можно использовать `var` вместо `let`, и тем самым мы сможем изменять наш объект типа `struct`:
```
if var car = possibleStructCar {
    car.name = "BMW"
}
```

Важно понимать, что область видимости заканчивается ровно там, где и начинается, т.е с `}` наш объект `car` уже недоступен:
```
if var car = possibleStructCar {
    car.name = "BMW"
}

car.name = "Mercedes"
// Error: Cannot find 'car' in scope
```
# Multiple optional bindings
Чтобы увидеть несколько **optional binding** в действии, назначим переменной `PossibleCar`, которая была определена в предыдущем разделе, новое значение **Car**.
```
var possibleCar: Car? = Car(name: "BMW")
```
Поскольку свойство name **Car** является опциональным, если мы хотим напечатать имя машины, которое мы инициализировали выше, мы должны использовать два **optional binding**, сначала для извлечения значения **Car**, а затем для извлечения его имени. Вот как это можно сделать с помощью вложенных необязательных привязок.
```
if let car = possibleCar, let name = car.name {
    print(name)
}
```
В зависимости от варианта использования, если эта дополнительная деталь представляет для нас интерес, мы можем использовать следующую функцию, которая использует вложенные **optional binding**, чтобы сначала проверить существование машины и, если машина найдена, проверить, имеет ли машина имя:
```
func checkForPossibleCarAndPossibleName(_ possibleCar: Car?) {
    if let car = possibleCar {
        print("Found a car")
        
        if let name = car.name {
            print("The car is named \(name)")
        } else {
            print("The car does not have a name")
        }
    } else {
        print("Did not find a car")
    }
}
```

Вот пример:
```
var possibleCar: Car? = Car(name: "BMW")

checkForPossibleCarAndPossibleName(possibleCar)
// Found a car
// The car is named BMW

possibleCar = Car()

checkForPossibleCarAndPossibleName(possibleCar)
// Found a car
// The car does not have a name

possibleCar = nil

checkForPossibleCarAndPossibleName(possibleCar)
// Did not find a car
```
# Optional binding c выходом
В ситуациях, когда нам нужно предпринять действия когда **optional binding** успешно прошла, так и когда она терпит неудачу, и мы имеем дело с несколькими опциональными типами, мы можем получить беспорядочный и неясный код с вложенными операторами **if-else**, как было показано в предыдущем разделе.
Предпочтительной альтернативой обычно является использование **guard** конструкций для обеспечения возможности раннего выхода, если не выполняются определенные условия. 
```
func checkForPossibleCarAndPossibleName2(_ possibleCar: Car?) {
    guard let car = possibleCar else {
        print("Did not find a car")
        return
    }
    
    print("Found a car")
    
    guard let name = car.name else {
        print("The car does not have a name")
        return
    }
    
    print("The car is named \(name)")
}
```
# Комбинация из optional binding и логических условий
**Optional binding** могут сочетаться с логическими условиями в любом порядке. 
Для оператора **if** с несколькими **optional binding** и несколькими логическими условиями первая ветвь оператора **if** будет выполнена только в том случае, если все **optional binding** будут успешными, 
а все логические условия будут иметь значение **true**.
Если какая-либо из **optional binding** не выполнена или логическое условие вычисляется как **false**, все последующие **optional binding** и логические условия игнорируются, первая ветвь инструкции **if** не выполняется, а любые инструкции в **else**, если имеются, выполняются.
```
func checkPossibleCar(_ possibleCar: Car?, forName nameToMatch: String) {
    if let car = possibleCar, let name = car.name, name == nameToMatch {
        print("Found a car named \(nameToMatch)")
    } else {
        print("Did not find a car named \(nameToMatch)")
    }
}
```
Протестируем эту функцию с помощью опционального значения машины с именем, которое мы ищем.
```
checkPossibleCar(possibleCar, forName: "BMW")
// Found a car named BMW
```
Если мы используем другое имя, например **Mercedes**, то **optional binding** пройдет успешно, но вот логическое условие `name == nameToMatch` будет **false** и тем самым у нас будет выведено на экран:
```
checkPossibleCar(possibleCar, forName: "Mercedes")
// Did not find a car named Mercedes
```
# Optional binding и цикл
**Optional binding** также может использоваться с циклами при использовании подхода, аналогичного тому, что мы видели с оператором **if**. 
Ключевое слово **let** используется после ключевого слова **while**, а инструкции в цикле **while** выполняются до тех пор, пока не будет выполнен **optional binding**. 
Как и в случае оператора **if**, любое количество **optional binding** и логических условий в любом порядке может использоваться для управления выполнением цикла **while**.
```
var possibleCars: [Car?] = Array(repeating: Car(isBusy: true), count: 3)

checkAllBusyCarsInArray(possibleCars)

func checkAllBusyCarsInArray(_ array: [Car?]) {
    var index = 0
    
    while let car = array[index], car.isBusy {
        index += 1
        
        if index == array.count {
            print("All busy cars found")
            return
        }
    }
    
    print("Did not find busy car at index \(index)")
}

// All busy cars found
```
Затем мы добавим в массив **optional** типа значение Car, которое имеет значение по умолчанию false для его свойства **isBusy**, и снова вызовем функцию.
```
possibleCars.append(Car())

checkAllBusyCarsInArray(possibleCars)

// Did not find busy car at index 3
```
# Optional binding vs. optional chaining
**Optional chaining** обеспечивает удобный и компактный механизм отправки сообщений в значение, которое является опциональным, включая настройку и получение значения свойства.
При наличии значения, операция по извлечению значения - выполняется успешно, а если нет, то **optional chain** терпит неудачу, не вызывая ошибок или сбоев. 
**Optional chaining** обеспечивает альтернативный способ вызова метода **run()** для значения Car, обернутого как опциональный тип, как показано ниже.
```
possibleCar?.run()
// Poehali
```
Аналогично, можно использовать **optional chaining** для задания значения свойства **name** для объекта **Car**, обернутого как опциональный тип.
```
possibleCar?.name = "New car"
```
Возникает вопрос, когда целесообразно использовать **optional binding** или **optional chaining**.
Первое соображение состоит в том, используем ли мы опциональное значение для отправки одного сообщения в упакованное значение или нам нужно использовать упакованное значение для нескольких операций. 
Для одной операции, вероятно, предпочтительна **optional chain**, как в приведенных выше примерах. 
Однако, если нам нужно обернутое значение для нескольких операций, может быть лучше и понятнее извлечь его с помощью **optional binding**.

Мы также должны рассмотреть, какие операции нам нужно выполнить. 
Для операций, которые стремятся мутировать обернутое значение, установка значения для свойства или вызов метода мутации, **optional chain** даст нам желаемый результат в одной операции, потому-что **optional chaining** действует непосредственно на обернутое значение. 
**Optional binding** создает копию обернутого значения, поэтому мы должны изменить эту копию, а затем заменить значение, обернутое как опциональное, новым значением для достижения того же эффекта. 
Мы только-что увидели, как можно использовать **optional chain** для назначения значения свойству **name** значения **Car**, для опционального значения **possibleCar**.
Вот как, такой же результат, может-быть достигнут с использованием **optional binding**.
```
var possibleCar: Car? = Car(name: "BMW")

if var car = possibleCar {
    car.name = "Mercedes"
    possibleCar = car
}
```
Важно отметить, что этот двухшаговый процесс требуется, когда обернутый тип является типом значения, таким как структура, в нашем примере.
Для типов значений опциональное значение переносит фактическое значение, и **optional binding** копирует это значение во временную константу или переменную, как это было сделано с переменной **car** выше. 
Таким образом, мы должны присвоить новое значение обратно для опционального объекта **possibleCar**.

Для класса, который является ссылочным типом, значение является только ссылкой на экземпляр класса. 
**Optional binding** создает копию этой ссылки, которую можно использовать для изменения экземпляра класса. 
Поскольку опциональный элемент по-прежнему содержит ссылку на тот же экземпляр класса, изменение автоматически отражается в опциональном элементе, как показано ниже:
```
var possibleCarClass: Car? = CarClass(name: "BMW")

if let carClass = possibleCarClass {
    carClass.name = "Mercedes"
}
```
#
Наконец, необходимо рассмотреть вопрос о том, будет ли обёрнутое значение использоваться в контексте, где опциональный параметр не может или не должен использоваться. 
В таких случаях **optional binding** является единственным способом, поскольку он разворачивает опциональное значение и извлекает обёрнутое значение, в то время как **optional chain** работает с обёрнутым значением без его разворачивания. 
Допустим, мы хотим напечатать значение, обёрнутое свойством **name** объекта **Car** и обёрнутого опциональным параметром **possibleCar**.
Если мы попробуем это с помощью **optional chain**, мы не получим предполагаемый выход, и мы получим предупреждение, потому что функция `print(_:)` ожидает не опциональный аргумент.
```
possibleCar = Car(name: "BWM")

print(possibleCar?.name)
// Optional("BWM")
// Warning: Expression implicitly coerced from 'String?' to 'Any'
```
В вышеописанном случае обходным решением является использование `??`, которое разворачивает опциональный параметр, если он имеет обёрнутое значение, и предоставляет значение по умолчанию в случае отсутствия обёрнутого значения, как также показано ниже.
```
possibleCar = Car(name: "BMW")

print(possibleCar?.name ?? "")
// BMW
```
# Использование optional chaining с optional binding
Хотя, иногда, нам приходится выбирать между **optional chaining** и **optional binding**, чаще они являются взаимодополняющими методами, которые можно использовать вместе. 
Как мы видели в предыдущем разделе, если мы хотим напечатать значение, обернутое свойством **name** значения **Car**, как опциональное, 
то мы можем использовать два **optional bindings** в том же самом операторе **if**, как показано ниже.
```
possibleCar: Car? = Car(name: "BMW")

if let car = possibleCar, let name = car.name {
    print(name)
}
// BMW
```
Т.к нам необходима машина и её имя, то мы можем сократить до:
```
if let name = possibleCar?.name {
    print(name)
}
```
# Заключение
**Опциональность** являются ключевой частью Swift, и **optional binding** является одним из механизмов, встроенных в язык, чтобы сделать опциональность проще и безопаснее в использовании. 
Мы можем использовать **optional binding** с оператором **if** или **guard**, в цикле **while**, объединять несколько **optional bindings** и несколько логических выражений в одной инструкции. 
Swift разрешает затенение имен с **optional binding**, что устраняет необходимость создания новых идентификаторов при развертывании опциональных значений. 
[В Swift 5.7](https://github.com/apple/swift-evolution/blob/main/proposals/0345-if-let-shorthand.md) это будет возможно с помощью более компактного и читаемого синтаксиса, где **optional binding** может также использоваться в сочетании с **optional chaining**, чтобы избежать ненужного размыкания опциональных значений.

