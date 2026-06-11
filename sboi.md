# Практика "Сбои"

## Описание предметной области

Система мониторинга устройств фиксирует сбои различных типов. Каждый сбой имеет тип, время возникновения и привязку к устройству. Сбои делятся на серьёзные (чётные типы) и несерьёзные (нечётные). Класс `ReportMaker` формирует отчёт об устройствах, имевших серьёзные сбои до указанной даты.

## Диаграмма классов

```mermaid
classDiagram
    
    class FailureType {
        UnexpectedShutdown
        ShortNonResponding
        HardwareFailures
        ConnectionProblems
    }
    
    <<enumeration>> FailureType

    class Device {
        +int Id
        +string Name
        +Device(int id, string name)
    }
    
    class Failure {
        +int DeviceId
        +FailureType Type
        +DateTime Time
        +Failure(int deviceId, FailureType type, DateTime time)
        +IsSerious() bool
    }
    
    class ReportMaker {
        +static List~string~ FindDevicesFailedBeforeDate(DateTime untilDate, Device[] devices, Failure[] failures)
        +static List~string~ FindDevicesFailedBeforeDateObsolete(int day, int month, int year, int[] failureTypes, int[] deviceId, object[][] times, List~Dictionary~ devices)
    }
    
    Failure --> FailureType : содержит тип сбоя
    ReportMaker ..> Device : извлекает имена устройств
    ReportMaker ..> Failure : анализирует список сбоев
```
