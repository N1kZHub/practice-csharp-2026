# Практика "Генератор отчетов"

## Описание предметной области

Вместо наследования применен паттерн делегирования через интерфейсы. Ответственность разделена на две части: форматирование вывода (HTML/Markdown) и вычисление статистических показателей (среднее с отклонением/медиана). Класс ReportMaker комбинирует форматтер и калькулятор для создания отчета.

## Диаграмма классов

```mermaid
classDiagram
    
    class Measurement {
        +double Temperature
        +double Humidity
    }
    
    class MeanAndStd {
        +double Mean
        +double Std
        +ToString() string
    }
    
    class IOutputFormatter {
        <<interface>>
        +FormatCaption(string) string
        +BeginList() string
        +FormatItem(string, string) string
        +EndList() string
    }
    
    class IStatisticsCalculator {
        <<interface>>
        +Calculate(IEnumerable~double~) object
    }
    
    class HtmlFormatter {
        +FormatCaption(string) string
        +BeginList() string
        +FormatItem(string, string) string
        +EndList() string
    }
    
    class MarkdownFormatter {
        +FormatCaption(string) string
        +BeginList() string
        +FormatItem(string, string) string
        +EndList() string
    }
    
    class MeanAndStdCalculator {
        +Calculate(IEnumerable~double~) object
    }
    
    class MedianCalculator {
        +Calculate(IEnumerable~double~) object
    }
    
    class ReportMaker {
        -IOutputFormatter _formatter
        -IStatisticsCalculator _calculator
        -string _caption
        +ReportMaker(IOutputFormatter, IStatisticsCalculator, string)
        +MakeReport(IEnumerable~Measurement~) string
    }
    
    class ReportMakerHelper {
        <<static>>
        +MeanAndStdHtmlReport(IEnumerable~Measurement~) string
        +MedianMarkdownReport(IEnumerable~Measurement~) string
        +MeanAndStdMarkdownReport(IEnumerable~Measurement~) string
        +MedianHtmlReport(IEnumerable~Measurement~) string
    }
    
    IOutputFormatter <|.. HtmlFormatter : реализует интерфейс
    IOutputFormatter <|.. MarkdownFormatter : реализует интерфейс
    IStatisticsCalculator <|.. MeanAndStdCalculator : реализует интерфейс
    IStatisticsCalculator <|.. MedianCalculator : реализует интерфейс
    
    ReportMaker *-- IOutputFormatter : композиция
    ReportMaker *-- IStatisticsCalculator : композиция
    
    ReportMaker ..> Measurement : обрабатывает данные
    MeanAndStdCalculator ..> MeanAndStd : возвращает результат
    
    ReportMakerHelper ..> ReportMaker : создаёт экземпляр
    ReportMakerHelper ..> HtmlFormatter : создаёт экземпляр
    ReportMakerHelper ..> MarkdownFormatter : создаёт экземпляр
    ReportMakerHelper ..> MeanAndStdCalculator : создаёт экземпляр
    ReportMakerHelper ..> MedianCalculator : создаёт экземпляр
```