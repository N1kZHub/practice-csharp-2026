# Практика: Fractal Painter

## Описание решения

Проведён рефакторинг приложения для рисования фракталов с целью устранения зависимости от сервис-локатора `Services`. Классы действий (`ImageSettingsAction`, `SaveImageAction`, `PaletteSettingsAction`) теперь получают все зависимости через конструкторы, что соответствует принципу инверсии зависимостей (DIP). Используется паттерн Factory Method через `Func<Window>` для отложенного получения родительского окна.

## Диаграмма классов

```mermaid
classDiagram

    class IUiAction {
        <<interface>>
        +MenuCategory Category
        +string Name
        +event CanExecuteChanged
        +CanExecute(param) bool
        +Execute(param) void
    }

    class MenuCategory {
        <<enumeration>>
        File
        Settings
        Fractals
    }

    class ImageSettingsAction {
        -IImageController controller
        -ImageSettings settings
        -Func~Window~ windowProvider
        +ImageSettingsAction(controller, settings, windowProvider)
        +Execute(param) void
    }

    class SaveImageAction {
        -IImageController controller
        -Func~Window~ windowProvider
        +SaveImageAction(controller, windowProvider)
        +Execute(param) void
    }

    class PaletteSettingsAction {
        -Palette colorPalette
        -Func~Window~ windowProvider
        +PaletteSettingsAction(colorPalette, windowProvider)
        +Execute(param) void
    }

    class DragonFractalAction {
        +Execute(param) void
    }

    class KochFractalAction {
        +Execute(param) void
    }

    class MainWindow {
        -SettingsManager settingsManager
        -Menu menu
        -ImageControl image
        +MainWindow(settingsManager, imageController)
    }

    class IImageController {
        <<interface>>
        +RecreateImage(settings) void
        +SaveImage(path) void
        +SetControl(control) void
    }

    class AvaloniaImageController {
        +RecreateImage(settings) void
        +SaveImage(path) void
        +SetControl(control) void
    }

    class ImageSettings {
        +Width
        +Height
    }

    class Palette {
        +Colors
    }

    class SettingsManager {
        +Load() AppSettings
    }

    class SettingsForm {
        +ShowDialog(parent) Task
    }

    class Window {
        <<Avalonia>>
    }

    IUiAction <|.. ImageSettingsAction
    IUiAction <|.. SaveImageAction
    IUiAction <|.. PaletteSettingsAction
    IUiAction <|.. DragonFractalAction
    IUiAction <|.. KochFractalAction
    IImageController <|.. AvaloniaImageController

    ImageSettingsAction --> IImageController : controller
    ImageSettingsAction --> ImageSettings : settings
    ImageSettingsAction ..> Window : через windowProvider
    ImageSettingsAction ..> SettingsForm : создаёт

    SaveImageAction --> IImageController : controller
    SaveImageAction ..> Window : через windowProvider

    PaletteSettingsAction --> Palette : colorPalette
    PaletteSettingsAction ..> Window : через windowProvider
    PaletteSettingsAction ..> SettingsForm : создаёт

    MainWindow --> SettingsManager : settingsManager
    MainWindow --> AvaloniaImageController : imageController
    MainWindow --> IUiAction : создаёт действия
    MainWindow --|> Window : наследует

    IUiAction --> MenuCategory : Category
```