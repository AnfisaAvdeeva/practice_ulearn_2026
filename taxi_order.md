# Практика: TaxiOrder

## 1. Описание предметной области и сущностей
Система управления заказами такси. Система позволяет клиентам создавать заказы, назначать водителей, отслеживать статус выполнения заказа, управлять поездкой и завершать её. Основными участниками процесса являются клиенты, водители и диспетчерская служба.

ValueType<T> - абстрактный базовый класс для всех объектов-значений, обеспечивает сравнение по содержимому свойств через Equals, GetHashCode, ToString

Entity<TId> - абстрактный базовый класс для всех сущностей, обеспечивает наличие Id и сравнение по нему

ITaxiApi<TOrder> - интерфейс, определяет контракт внешнего API для управления заказами такси

PersonName - объект-значение, хранит имя и фамилию клиента или водителя

Address - объект-значение, хранит улицу и номер дома для адреса отправления или назначения

Car - объект-значение, хранит модель, цвет и госномер автомобиля водителя

Driver - сущность водителя, содержит идентификатор, имя и автомобиль

TaxiOrder - корень агрегата, управляет жизненным циклом заказа такси, хранит клиента, адреса, водителя, статус и временные метки событий

DriversRepository - репозиторий для получения данных о водителях из базы данных по идентификатору

TaxiApi - фасадный класс, предоставляет внешний интерфейс, делегирует вызовы методам TaxiOrder

TaxiOrderStatus - перечисление состояний заказа: WaitingForDriver, WaitingCarArrival, InProgress, Finished, Canceled

## 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB
    
    class ValueType~T~ {
        <<abstract>>
        +Equals(object) bool
        +GetHashCode() int
        +ToString() string
        +Equals(T) bool
    }
    
    class Entity~TId~ {
        <<abstract>>
        -TId _id
        +Id TId
        +Equals(object) bool
        +GetHashCode() int
    }
    
    class ITaxiApi~TOrder~ {
        <<interface>>
        +CreateOrderWithoutDestination(firstName, lastName, street, building) TOrder
        +UpdateDestination(order, street, building) void
        +AssignDriver(order, driverId) void
        +UnassignDriver(order) void
        +GetDriverFullInfo(order) string
        +GetShortOrderInfo(order) string
        +Cancel(order) void
        +StartRide(order) void
        +FinishRide(order) void
    }
    
    class PersonName {
        +string FirstName
        +string LastName
        +PersonName(firstName, lastName)
        +ToString() string
    }
    
    class Address {
        +string Street
        +string Building
        +Address(street, building)
        +ToString() string
    }
    
    class Car {
        +string Model
        +string Color
        +string PlateNumber
        +Car(model, color, plateNumber)
    }
    
    class Driver {
        +int Id
        +PersonName Name
        +Car Car
        +Driver(id, name, car)
    }
    
    class TaxiOrder {
        +int Id
        +PersonName ClientName
        +Address Start
        +Address Destination
        +Driver Driver
        +TaxiOrderStatus Status
        +DateTime CreationTime
        +DateTime DriverAssignmentTime
        +DateTime CancelTime
        +DateTime StartRideTime
        +DateTime FinishRideTime
        +TaxiOrder(id, clientName, start, creationTime)
        +UpdateDestination(destination) void
        +AssignDriver(driver, time) void
        +UnassignDriver() void
        +Cancel(time) void
        +StartRide(time) void
        +FinishRide(time) void
        +GetDriverFullInfo() string
        +GetShortInfo() string
        -GetLastProgressTime() DateTime
    }
    
    class DriversRepository {
        +GetDriver(driverId) Driver
    }
    
    class TaxiApi {
        -DriversRepository driversRepo
        -Func~DateTime~ currentTime
        -int idCounter
        +TaxiApi(driversRepo, currentTime)
        +CreateOrderWithoutDestination(firstName, lastName, street, building) TaxiOrder
        +UpdateDestination(order, street, building) void
        +AssignDriver(order, driverId) void
        +UnassignDriver(order) void
        +GetDriverFullInfo(order) string
        +GetShortOrderInfo(order) string
        +Cancel(order) void
        +StartRide(order) void
        +FinishRide(order) void
    }
    
    class TaxiOrderStatus {
        <<enumeration>>
        WaitingForDriver
        WaitingCarArrival
        InProgress
        Finished
        Canceled
    }
    
    ValueType~T~ <|-- PersonName : наследует
    ValueType~T~ <|-- Address : наследует
    ValueType~T~ <|-- Car : наследует
    Entity~TId~ <|-- Driver : наследует
    Entity~TId~ <|-- TaxiOrder : наследует
    ITaxiApi~TaxiOrder~ <|.. TaxiApi : реализует
    
    Driver *-- PersonName : состоит из
    Driver *-- Car : состоит из
    
    TaxiOrder o-- PersonName : содержит клиента
    TaxiOrder o-- Address : содержит адрес отправления
    TaxiOrder o-- Address : содержит адрес назначения
    TaxiOrder o-- Driver : содержит водителя
    
    TaxiApi --> DriversRepository : использует репозиторий
    TaxiApi --> TaxiOrder : управляет заказом
    
    TaxiApi ..> PersonName : создаёт
    TaxiApi ..> Address : создаёт
    
    TaxiOrder --> TaxiOrderStatus : использует статус
```
