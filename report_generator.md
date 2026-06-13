# Практика: Генератор отчетов

## 1. Описание предметной области и сущностей
Система генерации статистических отчетов о погодных измерениях. Вычисляет статистические показатели (среднее, стандартное отклонение, медиану) по двум параметрам (температура и влажность) за несколько дней и выводит результаты в форматах HTML, Markdown.

Measurement - исходное погодное измерение с температурой и влажностью

MeanAndStd - результат вычисления, хранит среднее и стандартное отклонение

IStatisticsMaker - интерфейс вычислителя статистики, определяет название и метод вычисления

MeanAndStdStatistics - вычисляет среднее и стандартное отклонение, реализует IStatisticsMaker

MedianStatistics - вычисляет медиану набора чисел, реализует IStatisticsMaker

IReportFormatter - интерфейс форматтера отчета, определяет методы оформления (заголовок, списки, элементы)

HtmlReportFormatter - форматтер для HTML, реализует IReportFormatter

MarkdownReportFormatter - форматтер для Markdown, реализует IReportFormatter

ReportMaker - главный класс, комбинирует форматтер и вычислитель для создания отчета

ReportMakerHelper - статическая фабрика с 4 готовыми методами для всех комбинаций форматов и статистик
## 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB

    class IStatisticsMaker {
        <<interface>>
        +Caption string
        +MakeStatistics(data IEnumerable~double~) object
    }

    class IReportFormatter {
        <<interface>>
        +MakeCaption(caption string) string
        +BeginList() string
        +MakeItem(valueType string, entry string) string
        +EndList() string
    }

    class MeanAndStdStatistics {
        +Caption string
        +MakeStatistics(source IEnumerable~double~) object
    }

    class MedianStatistics {
        +Caption string
        +MakeStatistics(data IEnumerable~double~) object
    }

    class HtmlReportFormatter {
        +MakeCaption(caption string) string
        +BeginList() string
        +MakeItem(valueType string, entry string) string
        +EndList() string
    }

    class MarkdownReportFormatter {
        +MakeCaption(caption string) string
        +BeginList() string
        +MakeItem(valueType string, entry string) string
        +EndList() string
    }

    class ReportMaker {
        -formatter IReportFormatter
        -statisticsMaker IStatisticsMaker
        +ReportMaker(formatter IReportFormatter, statisticsMaker IStatisticsMaker)
        +MakeReport(measurements IEnumerable~Measurement~) string
    }

    class Measurement {
        +Temperature double
        +Humidity double
    }

    class MeanAndStd {
        +Mean double
        +Std double
        +ToString() string
    }

    class ReportMakerHelper {
        <<static>>
        +MeanAndStdHtmlReport(data IEnumerable~Measurement~) string
        +MedianMarkdownReport(data IEnumerable~Measurement~) string
        +MeanAndStdMarkdownReport(data IEnumerable~Measurement~) string
        +MedianHtmlReport(data IEnumerable~Measurement~) string
    }

    IStatisticsMaker <|.. MeanAndStdStatistics : реализует
    IStatisticsMaker <|.. MedianStatistics : реализует
    IReportFormatter <|.. HtmlReportFormatter : реализует
    IReportFormatter <|.. MarkdownReportFormatter : реализует

    ReportMaker *-- IReportFormatter : состоит из (передан в конструктор)
    ReportMaker *-- IStatisticsMaker : состоит из (передан в конструктор)


    MeanAndStdStatistics o-- MeanAndStd : возвращает как результат

    ReportMakerHelper ..> ReportMaker : создает экземпляр
    ReportMakerHelper ..> HtmlReportFormatter : создает экземпляр
    ReportMakerHelper ..> MarkdownReportFormatter : создает экземпляр
    ReportMakerHelper ..> MeanAndStdStatistics : создает экземпляр
    ReportMakerHelper ..> MedianStatistics : создает экземпляр

    ReportMaker ..> Measurement : принимает как параметр
    ReportMaker ..> MeanAndStd : вызывает ToString()
```
