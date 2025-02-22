---
title: Документация
description: 
extends: _layouts.documentation.ru
section: main
---

## Добро пожаловать

Это руководство содержит справочную информацию для создания бизнес-приложений с использованием платформы, при этом 
не поясняет документацию фреймворка. Если вы уже знакомы с ним, перейдите к прочтению следующих глав, иначе 
рекомендую прочитать документацию «Laravel» от начала до конца на [laravel.su](http://laravel.su/docs). 


> Для предложения улучшений этого руководства, [создайте новый issue](https://github.com/orchidsoftware/orchid.software/issues). 
При появлении вопросов или нахождения ошибки по документации, пожалуйста, укажите главу и сопутствующий текст, что бы указать на ошибку.


## Введение

**ORCHID** - это инструмент [ускоренной разработки](https://ru.wikipedia.org/wiki/RAD_(%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5)) с открытым исходным кодом (лицензия MIT), для создания приложений в стиле администрирования. Он абстрагирует общие шаблоны бизнес-приложений, чтобы разработчикам было легко реализовывать красивые и элегантные интерфейсы без особых усилий. 

Некоторые особенности:

- **Построитель форм** - нет нужны описывать однотипные HTML поля каждый раз.
- **Экраны** - хороший баланс между CRUD генерацией и ручным написанием.
- **Поля** - более 40 видов.
- **Разделение прав доступа** - предлагает удобное управление в разработке и поддержки.
- Меню, графики, настройки, уведомления, и т.п. уже сейчас.

Поставляется в виде пакета для Laravel и взаимодействует с другими компонентами. Может выступать в качестве основы для backoffice-приложений, панелей администрирования или выполнять функции системы управления контентом. 


## Почему быстрая разработка?

Классическое веб приложение представляет собой подсистему с общей трёхъярусной архитектурой, которая состоит из:

- **Презентационного уровня** - графический интерфейс представленный пользователю (браузеру), включая javascript сценарии, стили и ресурсы.

- **Уровня прикладной логики** - в нашем случаи это фреймворк - связующее звено, где сосредоточена большая часть бизнес-логики, работа с базой данных (Eloquent), отправка ресурсов и различная обработка.

- **Уровня управления ресурсами** - обеспечивает хранение данных, как правило реализуется средствами систем управления базами данных (MySQL,PostgreSQL,Microsoft SQL Server,SQLite).
 
 
![Architecture](https://orchid.software/assets/img/scheme/architecture.jpg)

Сокращение времени разработки непосредственно связано с распределением обязанностей между каждым из уровней. Это особенно заметно, когда необходимо создавать вспомогательный код, в то время как, большую часть действительно полезной работы берёт на себя прикладной слой.

Как различные примеры противопоставления обязанностями можно привести:
- Генерация `HTML` шаблонизатором `Blade` или фреймворком `Vue`.
- Использование `ORM` или хранимых процедур.

В зависимости от выбора решений будут и распределены обязанности, где у каждого решения есть как плюсы так и минусы.

Точно так же, платформа наделяет прикладной уровень новыми обязанностями по управлению отображением и прокладыванию моста к данным.

```php
Classic          |   Orchid
├── Route        |   ├── Route   
├── Model        |   ├── Model 
├── Controller   |   └── Screen
└── View         |
    ├── HTML     |
    ├── CSS      |
    └── JS       |
```

## Как получить платформу?

Платформа свободно распространяется через интернет, [исходные коды](https://github.com/orchidsoftware/platform) и [информация о выпусках](https://github.com/orchidsoftware/platform/releases) опубликованы на GitHub. В руководстве по [установке](/ru/docs/installation/) содержатся подробные инструкции. 
