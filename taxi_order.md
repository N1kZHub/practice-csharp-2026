# Практика "TaxiOrder: Предметно-ориентированное проектирование"

## Описание предметной области

Система управления заказами такси, построенная на принципах DDD. 
Бизнес-логика инкапсулирована в доменных объектах, а технические 
детали вынесены в инфраструктурные компоненты. 
Агрегат `TaxiOrder` управляет жизненным циклом заказа, валидирует 
переходы состояний и координирует взаимодействие с водителем. 
Сервисный слой (`TaxiApi`) выступает тонкой прослойкой, 
делегирующей выполнение доменным моделям.

## Диаграмма классов

```mermaid
classDiagram
    
    class ITaxiApi~TOrder~ {
        <<interface>>
        +CreateOrderWithoutDestination(...) TOrder
        +UpdateDestination(TOrder, street, building) void
        +AssignDriver(TOrder, driverId) void
        +UnassignDriver(TOrder) void
        +Cancel(TOrder) void
        +StartRide(TOrder) void
        +FinishRide(TOrder) void
        +GetDriverFullInfo(TOrder) string
        +GetShortOrderInfo(TOrder) string
    }
    
    class TaxiApi {
        -DriversRepository _driversRepo
        -Func~DateTime~ _currentTime
        -int _idCounter
        +CreateOrderWithoutDestination(...) TaxiOrder
        +UpdateDestination(order, street, building) void
        +AssignDriver(order, driverId) void
        +UnassignDriver(order) void
        +Cancel(order) void
        +StartRide(order) void
        +FinishRide(order) void
        +GetDriverFullInfo(order) string
        +GetShortOrderInfo(order) string
    }
    
    class TaxiOrder {
        <<Entity~int~~
        -DriversRepository _driversRepo
        -Func~DateTime~ _currentTime
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
        +CreateWithoutDestination(...) TaxiOrder$
        +UpdateDestination(Address) void
        +AssignDriver(int) void
        +UnassignDriver() void
        +Cancel() void
        +StartRide() void
        +FinishRide() void
        +GetDriverFullInfo() string
        +GetShortOrderInfo() string
    }
    
    class Driver {
        <<Entity~int~~
        +PersonName Name
        +Car Car
    }
    
    class Car {
        <<ValueType~Car~~
        +string Model
        +string Color
        +string PlateNumber
    }
    
    class PersonName {
        <<ValueType~PersonName~~
        +string FirstName
        +string LastName
    }
    
    class Address {
        <<ValueType~Address~~
        +string Street
        +string Building
    }
    
    class DriversRepository {
        +GetDriverInfo(int) DriverInfo
    }
    
    class DriverInfo {
        +string FirstName
        +string LastName
        +string CarModel
        +string CarColor
        +string CarPlateNumber
    }
    
    class TaxiOrderStatus {
        <<enumeration>>
        WaitingForDriver
        WaitingCarArrival
        InProgress
        Finished
        Canceled
    }
    
    class Entity~TId~ {
        <<abstract>>
        +TId Id
    }
    
    class ValueType~T~ {
        <<abstract>>
        +Equals(T) bool
        +GetHashCode() int
        +ToString() string
    }
    
    ITaxiApi~TaxiOrder~ <|.. TaxiApi : реализует контракт
    Entity~int~ <|-- TaxiOrder : сущность
    Entity~int~ <|-- Driver : сущность
    ValueType~Car~ <|-- Car : объект-значение
    ValueType~PersonName~ <|-- PersonName : объект-значение
    ValueType~Address~ <|-- Address : объект-значение
    
    TaxiApi o-- DriversRepository : инфраструктура
    TaxiOrder *-- PersonName : клиент
    TaxiOrder *-- Address : точки маршрута
    TaxiOrder o-- Driver : назначенный водитель
    Driver *-- PersonName : ФИО
    Driver *-- Car : транспорт
    TaxiOrder --> TaxiOrderStatus : текущее состояние
    DriversRepository ..> DriverInfo : DTO для поиска
    TaxiApi ..> TaxiOrder : фабрика заказов
```