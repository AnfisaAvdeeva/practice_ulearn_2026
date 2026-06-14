# Практика: Роботы

## 1. Описание предметной области и сущностей
Система управления роботами, состоит из двух ключевых компонентов: интеллектуальной системы управления (AI) и исполнительного устройства (Device). AI генерирует команды, а Device их выполняет.

IRobotAI<TCommand> - интерфейс искусственного интеллекта робота. Определяет метод получения команды для выполнения.

RobotAI<TCommand> - абстрактный базовый класс искусственного интеллекта. Содержит общий контракт для генерации команд и служит основой для реализаций AI.

ShooterAI - реализация искусственного интеллекта боевого робота. Генерирует команды типа IShooterMoveCommand.

BuilderAI - реализация искусственного интеллекта строительного робота. Генерирует команды типа IMoveCommand.

IDevice<TCommand> - интерфейс устройства робота. Определяет метод выполнения полученной команды.

Device<TCommand> - абстрактный базовый класс устройства. Задает общий контракт для исполнения команд различных типов.

Mover - устройство перемещения робота. Выполняет команды типа IMoveCommand.

ShooterMover - устройство перемещения боевого робота. Выполняет команды типа IShooterMoveCommand.

Robot<TCommand> - основной класс робота. Объединяет искусственный интеллект и устройство, организуя процесс получения и выполнения команд.

Robot - статический класс. Предоставляет метод Create, который создает экземпляры роботов.

## 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram

class IRobotAI~TCommand~ {
    <<interface>>
    +GetCommand() TCommand
}

class RobotAI~TCommand~ {
    <<abstract>>
    +GetCommand() TCommand
}

class ShooterAI {
    -counter : int
    +GetCommand() IShooterMoveCommand
}

class BuilderAI {
    -counter : int
    +GetCommand() IMoveCommand
}

class IDevice~TCommand~ {
    <<interface>>
    +ExecuteCommand(command) string
}

class Device~TCommand~ {
    <<abstract>>
    +ExecuteCommand(command) string
}

class Mover {
    +ExecuteCommand(command) string
}

class ShooterMover {
    +ExecuteCommand(command) string
}

class Robot_TCommand_ {
    -ai : IRobotAI~TCommand~
    -device : IDevice~TCommand~
    +Robot(ai, executor)
    +Start(steps) IEnumerable~string~
}

class Robot {
    <<static>>
    +Create~TCommand~(ai, executor)
}

IRobotAI~TCommand~ <|.. RobotAI~TCommand~ : реализует интерфейс
RobotAI~TCommand~ <|-- ShooterAI : наследуется
RobotAI~TCommand~ <|-- BuilderAI : наследуется

IDevice~TCommand~ <|.. Device~TCommand~ : реализует интерфейс
Device~TCommand~ <|-- Mover : наследуется
Device~TCommand~ <|-- ShooterMover : наследуется

Robot_TCommand_ o-- IRobotAI~TCommand~ : получает команды
Robot_TCommand_ o-- IDevice~TCommand~ : выполняет команды

Robot ..> Robot_TCommand_ : создаёт через Create()
```
