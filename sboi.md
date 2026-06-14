# Практика: Сбои 

## 1. Описание предметной области и сущностей
Система предназначена для анализа критических сбоев оборудования. Необходимо определить, какие устройства имели серьёзные сбои до определённой даты.

Device - устройство, хранит идентификатор DeviceId и название Name.

Failure - содержит тип сбоя FailureType, идентификатор устройства DeviceId и дату Date. Предоставляет методы IsSerious() и IsBeforeDate().

FailureType - типы сбоев UnexpectedShutdown (0), ShortNonResponding (1), HardwareFailures (2), ConnectionProblems (3).

ReportMaker - статический метод FindDevicesFailedBeforeDate принимает дату, одно устройство Device и один сбой Failure. 

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
        +int DeviceId$
        +string Name$
    }

    class Failure {
        -FailureType type
        -int deviceId
        -DateTime date
        +Failure(FailureType type, int deviceId, DateTime date)
        +bool IsSerious()
        +bool IsBeforeDate(DateTime targetDate)
        +FailureType Type$
        +int DeviceId$
        +DateTime Date$
    }

    class ReportMaker {
        +List~string~ FindDevicesFailedBeforeDate(DateTime targetDate, Device device, Failure failure)$
        +List~string~ FindDevicesFailedBeforeDateObsolete(int day, int month, int year, int[] failureTypes, int[] deviceId, object[] times, List~Dictionary~string, object~~ devices)$
    }

    ReportMaker ..> Failure : Зависимость
    ReportMaker ..> Device : Зависимость
    ReportMaker ..> FailureType : Зависимость
    Failure ..> FailureType : Зависимость
```
