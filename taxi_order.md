# Практика: TaxiOrder

## 1. Описание предметной области и сущностей
Система управления заказами такси. Система позволяет клиентам создавать заказы, назначать водителей, отслеживать статус выполнения заказа, управлять поездкой и завершать её. Основными участниками процесса являются клиенты, водители и диспетчерская служба.

TaxiOrder - управляет жизненным циклом заказ, хранит клиента, адреса, водителя, статус и временные метки событий. Инкапсулирует бизнес-логику переходов между состояниями

Driver - сущность водителя с идентификатором, именем и автомобилем. Может существовать независимо от заказов

PersonName - объект-значение, хранит имя и фамилию человека. Используется для клиентов и водителей

Address - объект-значение, хранит улицу и номер дома для адресов отправления и назначения

Car - объект-значение, описывает автомобиль, его модель, цвет и госномер

TaxiOrderStatus - перечисление состояний заказа: ожидание водителя, ожидание прибытия, в пути, завершен, отменен

DriversRepository - репозиторий для получения водителей из БД по идентификатору

TaxiApi - предоставляет внешний API и делегирует вызовы методам TaxiOrder

ValueType\<T\> - базовый класс для объектов-значений, реализует сравнение по содержимому свойств

Entity\<TId\> - базовый класс для сущностей с уникальным идентификатором

ITaxiApi\<TOrder\> - интерфейс внешнего API для управления заказами такси

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
