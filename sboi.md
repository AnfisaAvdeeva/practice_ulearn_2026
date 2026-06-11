# Практика: Сбои 

## 1. Описание предметной области и сущностей
Device - устройство, хранит идентификатор устройства DeviceId и его название Name. 
Failure - сбой, включает тип сбоя FailureType, DeviceId и дату возникновения Date. Предоставляет методы IsSerious() для определения серьёзности сбоя и IsBeforeDate() для проверки даты.
FailureType - четыре типа сбоев UnexpectedShutdown (0), ShortNonResponding (1), HardwareFailures (2), ConnectionProblems (3). 
ReportMaker - отчёт об устройствах с критическими сбоями до указанной даты. Новый метод принимает дату, массив сбоев и массив устройств. 

## 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB

    class FailureType {
        <<enumeration>>
        UnexpectedShutdown
        ShortNonResponding
        HardwareFailures
        ConnectionProblems
    }

    class Device {
        -int deviceId
        -string name
        +Device(int deviceId, string name)
        +DeviceId int$
        +Name string$
    }

    class Failure {
        -FailureType failureType
        -int deviceId
        -DateTime date
        +Failure(FailureType failureType, int deviceId, DateTime date)
        +IsSerious() bool
        +IsBeforeDate(DateTime targetDate) bool
        +FailureType FailureType$
        +DeviceId int$
        +Date DateTime$
    }

    class ReportMaker {
        +FindDevicesFailedBeforeDate(DateTime targetDate, Failure[] failures, Device[] devices) List~string~
        +FindDevicesFailedBeforeDateObsolete(int day, int month, int year, int[] failureTypes, int[] deviceId, object[][] times, List~Dictionary~string, object~~ devices) List~string~
    }

    ReportMaker ..> Failure : Зависимость(параметр метода)
    ReportMaker ..> Device : Зависимость(параметр метода)
    Failure --> Device : Ассоциация(DeviceId в поле)
    Failure --> FailureType : Ассоциация(FailureType в поле)
```
