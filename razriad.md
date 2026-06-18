# Практика: Контрольный разряд

## Описание решения

Реализованы алгоритмы вычисления контрольного разряда (UPC, ISBN-10, Luhn) с применением принципа единственной ответственности. Общая логика вынесена в extension методы (`ExtractDigits`, `CalculateModuloComplement`, `DoubleWithCorrection`), а специфичная - в приватные методы вычисления контрольных сумм.

## Диаграмма классов

```mermaid
classDiagram
    direction TB

    %% Extension методы (общий код)
    class Extensions {
        <<static>>
        +ExtractDigits() IEnumerable~int~
        +CalculateModuloComplement(sum, modulo) int
        +DoubleWithCorrection() int
    }

    %% Публичный API
    class ControlDigitAlgo {
        <<static>>
        +Upc(number) int
        +Isbn10(number) char
        +Luhn(number) int
    }

    %% Приватные методы
    class ComputeUpcChecksum {
        <<private>>
        -useThree: bool
        +Compute(digits) int
    }

    class ComputeIsbn10Checksum {
        <<private>>
        -multiplier: int
        +Compute(digits) int
    }

    class ComputeLuhnChecksum {
        <<private>>
        +Compute(digits) int
    }

    class FormatIsbn10CheckDigit {
        <<private>>
        +Format(digit) char
    }

    %% Связи
    ControlDigitAlgo ..> Extensions : использует extension методы
    ControlDigitAlgo ..> ComputeUpcChecksum : вызывает
    ControlDigitAlgo ..> ComputeIsbn10Checksum : вызывает
    ControlDigitAlgo ..> ComputeLuhnChecksum : вызывает
    ControlDigitAlgo ..> FormatIsbn10CheckDigit : вызывает
    
    Extensions ..> long : расширяет
    Extensions ..> int : расширяет
```