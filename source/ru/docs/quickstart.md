---
title: Быстрый старт для начинающих
description: Краткое руководство представляет собой базовое введение в инфраструктуру Orchid
extends: _layouts.documentation.ru
section: main
---

Это краткое руководство представляет собой базовое введение в инфраструктуру и включает в себя информацию
об экранах и элементах формы. 
Чтобы отобрать базовый набор функций, мы создадим простую утилиту по отправке сообщений по электронной почте.

На этом этапе необходимо, что бы вы уже [установили фреймворк и платформу](/ru/docs/installation), создали базу данных и запустили веб-сервер.

Для начала необходимо создать [экран](/ru/docs/screens) для отображения формы отправки, с помощью команды:

```bash
php artisan orchid:screen EmailSenderScreen
```

В директории `app/Orchid/Screens` будет создан новый файл `EmailSenderScreen.php` :

```php
namespace App\Orchid\Screens;

use Orchid\Screen\Screen;

class EmailSenderScreen extends Screen
{
    /**
     * Свойства имя и описание отвечают за то, 
     * какое название будет отображаться 
     * на экране пользователя и в заголовках.
     */
    public $name = 'EmailSenderScreen';
    public $description = 'EmailSenderScreen';

    /**
     * Метод определяющие все входные данные экрана, 
     * именно в нём должны вызываться запросы к базе данных,
     * api или любые другие (не обязательно явно),
     * результатом которого должен быть массив, 
     * обращение к которым будут использоваться его ключи.
     */
    public function query(): array
    {
        return [];
    }

    /**
     * Определяет управляющие кнопки и события
     * которые должны будут произойти по нажатию
     */
    public function commandBar(): array
    {
        return [];
    }

    /**
     * Набор отображений, 
     * строк, таблицы, графиков,
     * модальных окон и их комбинации
     */
    public function layout(): array
    {
        return [];
    }
}
```

Главным отличием экрана от контроллера, является определённая заранее структура, которая обслуживает 
только одну страницу, определяя данные и события.

Как и контроллер, экран нужно регистрировать в файле маршрутов, 
определим его в файле для панели администрирования `routes/platform.php`:

```php
use App\Orchid\Screens\EmailSenderScreen;

$this->router->screen('email', EmailSenderScreen::class)->name('platform.email');
```

После того как мы зарегистрировали новый маршрут, можно перейти в браузере по адресу `/dashboard/email`, 
что бы посмотреть на пустой экран, наполним же его элементами.

Добавим название и описание:

```php
/**
 * Display header name.
 *
 * @var string
 */
public $name = 'Email sender';

/**
 * Display header description.
 *
 * @var string
 */
public $description = 'Tool that sends ad-hoc email messages.';
```

Для того, что бы отобразить поля для ввода, мы опишем их в методе `Layouts`:

```php
/**
 * Views.
 *
 * @return Layout[]
 */
public function layout(): array
{
    return [
        Layout::rows([
            Input::make('subject')
                ->title('Subject')
                ->required()
                ->placeholder('Message subject line')
                ->help('Enter the subject line for your message'),

            Relation::make('users.')
                ->title('Recipients')
                ->multiple()
                ->required()
                ->placeholder('Email addresses')
                ->help('Enter the users that you would like to send this message to.')
                ->fromModel(User::class,'name','email'),

            Quill::make('content')
                ->title('Content')
                ->required()
                ->placeholder('Insert text here ...')
                ->help('Add the content for the message that you would like to send.')

        ])->with(70)
    ];
}
```

Теперь наша страница имеет некоторые элементы, но не производит действий, 
что бы определить их необходимо создать новый публичный метод и указать ссылку на него в `commandBar`:

```php
namespace App\Orchid\Screens;

use App\User;
use Illuminate\Http\Request;
use Illuminate\Mail\Message;
use Illuminate\Support\Facades\Mail;
use Orchid\Screen\Fields\Input;
use Orchid\Screen\Fields\Quill;
use Orchid\Screen\Fields\Relation;
use Orchid\Screen\Layout;
use Orchid\Screen\Link;
use Orchid\Screen\Screen;
use Orchid\Support\Facades\Alert;

class EmailSenderScreen extends Screen
{
    /**
     * Display header name.
     *
     * @var string
     */
    public $name = 'Email sender';

    /**
     * Display header description.
     *
     * @var string
     */
    public $description = 'Tool that sends ad-hoc email messages.';

    /**
     * Query data.
     *
     * @return array
     */
    public function query(): array
    {
        return [];
    }

    /**
     * Button commands.
     *
     * @return Link[]
     */
    public function commandBar(): array
    {
        return [
            Link::name('Send Message')
                ->icon('icon-paper-plane')
                ->method('sendMessage')
        ];
    }

    /**
     * Views.
     *
     * @return Layout[]
     */
    public function layout(): array
    {
        return [
            Layout::rows([
                Input::make('subject')
                    ->title('Subject')
                    ->required()
                    ->placeholder('Message subject line')
                    ->help('Enter the subject line for your message'),

                Relation::make('users.')
                    ->title('Recipients')
                    ->multiple()
                    ->required()
                    ->placeholder('Email addresses')
                    ->help('Enter the users that you would like to send this message to.')
                    ->fromModel(User::class,'name','email'),

                Quill::make('content')
                    ->title('Content')
                    ->required()
                    ->placeholder('Insert text here ...')
                    ->help('Add the content for the message that you would like to send.')

            ])->with(70)
        ];
    }

    /**
     * @param Request $request
     *
     * @return \Illuminate\Http\RedirectResponse
     */
    public function sendMessage(Request $request)
    {
        Mail::raw($request->get('content'), function (Message $message) use ($request) {

            $message->subject($request->get('subject'));

            foreach ($request->get('users') as $email) {
                $message->to($email);
            }
        });


        Alert::info('Your email message has been sent successfully.');
        return back();
    }
}
```

После этого мы имеем возможность отправлять email сообщения на адреса.

> Обратите внимание, по умолчанию включен `smtp` драйвер для отправки почты, 
вы можете изменить его в `.env` файле на `log` для проверки.

Так как наша утилита вымешенная, мы можем пофантазировать и представить, что наш босс попросил ставить заголовок наших сообщений вида "Новости кампании за июль", но иметь возможность менять его. Для этого добавим ключ в метод `query` с именем нашего элемента:

```php
/**
 * Query data.
 *
 * @return array
 */
public function query(): array
{
    return [
        'subject' => date('F').' Campaign News',
    ];
}
```

Теперь в поле с именем `subject` автоматически подстанавливается значение из результата.

Все это время для отображения экрана приходилось в браузере указывать явную страницу, добавим новый пункт в меню, для этого
в файле композера по адресу `app/Orchid/Composers/MainMenuComposer.php` добавим объявление:

```php
$this->dashboard->menu
    ->add(Menu::MAIN,
        ItemMenu::label('Email sender')
            ->icon('icon-envelope-letter')
            ->route('platform.email')
            ->title('Tools')
    );
```

Теперь наша утилита отображается в левом меню и активна при посещении. 
Навигация может осуществляться не только посредством переходов из меню, но и через хлебные крошки, 
что бы добавить их к нашему экрану необходимо в `routes/breadcrumbs.php` добавить новое объявление по имени маршрута:

```php
Breadcrumbs::for('platform.email', function ($trail) {
    $trail->parent('platform.index');
    $trail->push('Email sender');
});
```


Поздравляем, теперь вы должны понимать, как работает платформа! Это очень простой пример, но процесс разработки будет идентичен во многих аспектах. Теперь рекомендуем перейти к разделу ["Экраны"](/ru/docs/screens), что бы узнать больше о возможностях которые находятся у вас в руках.
