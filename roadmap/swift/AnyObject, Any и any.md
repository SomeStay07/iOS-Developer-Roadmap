# AnyObject и Any получили новый вариант any
Как было представлено в [SE-355](https://github.com/apple/swift-evolution/blob/main/proposals/0335-existential-any.md), что затрудняет нам, разработчикам, понять различия. 
Каждый параметр имеет свои сценарии использования и подводные камни относительно того, когда не использовать их.

`Any` и `AnyObject` являются специальными типами в Swift, используемыми для стирания типов, и не имеют прямой связи ни с одним из них.

# Когда использовать `AnyObject?`
`AnyObject` - это протокол, которому неявно соответствуют все классы. Фактически стандартная библиотека содержит псевдоним типа `AnyClass`, представляющий `AnyObject.Type`.

  - ```print(AnyObject.self) // Prints: AnyObject```
  - ```print(AnyClass.self) // Prints: AnyObject.Type```

Все классы, типы классов или протоколы только для классов, могут использовать `AnyObject` в качестве конкретного типа. Для демонстрации можно создать массив различных типов:

```
let imageView = UIImageView(image: nil)
let viewController = UIViewController(nibName: nil, bundle: nil)

let mixedArray: [AnyObject] = [
    // We can add both `UIImageView` and `UIViewController` to the same array
    // since they both cast to `AnyObject`.
    imageView,
    viewController,

    // The `UIViewController` type conforms implicitly to `AnyObject` and can be added as well.
    UIViewController.self
]
```

Только классы соответствуют `AnyObject`, что означает, что их можно использовать для ограничения реализации протоколов только ссылочными типами:

`protocol MyProtocol: AnyObject { }`

`AnyObject` можно использовать, если требуется гибкость нетипизированного объекта. 

```
func configureImage(_ image: UIImage, in imageDestinations: [AnyObject]) {
    for imageDestination in imageDestinations {
        switch imageDestination {
        case let button as UIButton:
            button.setImage(image, for: .normal)
        case let imageView as UIImageView:
            imageView.image = image
        default:
            print("Unsupported image destination")
            break
        }
    }
}
```

Используя `AnyObject` в качестве целевого объекта, мы всегда должны приводить и помнить о фейле при кастинге, с использованием реализации по умолчанию. 
Пример с использованием конкретных протоколов:
```
// Create a protocol to act as an image destination.
protocol ImageContainer {
    func configureImage(_ image: UIImage)
}

// Make both `UIButton` and `UIImageView` conform to the protocol.
extension UIButton: ImageContainer {
    func configureImage(_ image: UIImage) {
        setImage(image, for: .normal)
    }
}

extension UIImageView: ImageContainer {
    func configureImage(_ image: UIImage) {
        self.image = image
    }
}

// Create a new method using the protocol as a destination.
func configureImage(_ image: UIImage, into destinations: [ImageContainer]) {
    for destination in destinations {
        destination.configureImage(image)
    }
}
```

Полученный код является более чистым, читаемым и больше не требует обработки неподдерживаемых контейнеров. 
Экземпляры должны соответствовать протоколу ImageContainer для получения настроенного образа.

# Когда использовать `Any`?
`Any` может представлять экземпляр любого типа, включая типы функций:
```
let arrayOfAny: [Any] = [
    0,
    "string",
    { (message: String) -> Void in print(message) }
]
```

Те же правила применяются к `Any` по сравнению с `AnyObject`, что означает, что вы всегда должны стремиться использовать конкретные типы. 
`Any` является более гибким, позволяя отливать экземпляры любого типа, что затрудняет прогнозирование кода по сравнению с использованием конкретных типов.

# Когда использовать `any`?
`any` выглядит аналогично `Any` и `AnyObject`, но имеет другую цель, поскольку вы используете его для обозначения использования экзистенциального. 
Следующий пример кода демонстрирует конфигуратор изображения, использующий пример кода из предыдущих примеров в этой статье:
```
struct ImageConfigurator {
    var imageContainer: any ImageContainer

    func configureImage(using url: URL) {
        // Note: This is not the way to efficiently download images
        // and is just used as a quick example.
        let image = UIImage(data: try! Data(contentsOf: url))!
        imageContainer.configureImage(image)
    }
}

let iconImageView = UIImageView()
var configurator = ImageConfigurator(imageContainer: iconImageView)
configurator.configureImage(using: URL(string: "https://picsum.photos/200/300")!)
let image = iconImageView.image
```
Как вы видите, мы указали использование экзистенциального `ImageContainer`, пометив наше свойство `imageContainer` ключевым словом `any`.
Маркировка протокола с использованием `any` протокола будет применяться начиная с 6 Swift'a, поскольку она будет указывать влияние на производительность использования протокола таким образом.

Экзистенциальные типы имеют существенные ограничения и последствия для производительности и являются более дорогими, чем использование конкретных типов, поскольку их можно изменять динамически. Примером такого изменения является следующий код:
```
let button = UIButton()
configurator.imageContainer = button
```
Наше свойство `imageContainer` может представлять любое значение, соответствующее нашему протоколу `ImageContainer`, и позволяет нам изменить его с изображения на кнопку. 
Для этого требуется динамическая память, что лишает компилятор возможности оптимизировать этот фрагмент кода. 
Вплоть до введения `any` ключевого слова не было явного указания разработчикам на эту стоимость производительности.

# Уход от `any`
Мы можем переписать приведенный выше пример кода с помощью дженериков и избавиться от потребности в динамической памяти:
```
struct ImageConfigurator<Destination: ImageContainer> {
    var imageContainer: Destination
}
```
Осознание последствий для производительности и знание того, как переписать код, является необходимым навыком для работы в качестве разработчика Swift.

# Вывод

`Any`, `any` и `AnyObject` выглядят аналогично, но имеют важные различия. На мой взгляд, лучше переписать свой код и отнять необходимость использовать любое из этих ключевых слов. Это часто приводит к более удобочитаемому и предсказуемому коду.

# Материал

[Ссылка на оригинал](https://www.avanderlee.com/swift/anyobject-any/)
