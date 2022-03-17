# StreamTelecom

This package was developed based on the stream-telecom.ru API (https://stream-telecom.ru/solutions/integrations/).

The following methods are implemented in this package:
1. View balance;
2. Unloading tariffs;
3. Unloading statistics;
4. HLR requests;
5. Sending SMS;
6. Sending messages to Viber;
7. Sending notifications to VK;
8. Email sending;

## Installation
```bash
composer require manzadey/stream-telecom
```

## Beginning of work
```php
use Manzadey\StreamTelecom\StreamTelecom;

$st = new StreamTelecom('name', 'login', 'password');
```

## View balance
```php
echo $st->balance();
```

## Uploading tariffs
```php
echo $st->tariffs();
```
Cost of specific requests
```php
echo $st->tariffs()->hlr();
echo $st->tariffs()->email();
echo $st->tariffs()->messenger();
```
## Upload statistics
```php
$st->statistic()->start('02/02/2020 10:00')->end('03/03/2020 10:00')->get();
```
To unload detailed statistics, you must specify the `detail()` method;

```php
$st->statistic()->start('02/02/2020 10:00')->end('03/03/2020 10:00')->detail()->get();
```

The response will be the ID of the report.

**Optional methods for detailed statistics:**

`state()` - Formation of statistics on a certain status of messages. Valid values ​​are deliver, not_deliver, expired, sent (delivered, not delivered, expired, in progress).

`phone()` - Formation of statistics for a specific subscriber number.

`cBase()` - Indicates the need to upload data with reference to the database of recipients.

`subStat()` - Indicates the need to upload data from sublogins.

`typeLoad()` - Select the report format. Valid values: 0, 1 (0 -csv default, 1 -xls)

```php
$st->statistic()
->start('02/02/2020 10:00')
->end('03/03/2020 10:00')
->detail()
->state('not_deliver')
->phone(79111234567)
->cBase()
->subStat()
->typeLoad(1)
->get();
```

## HLR
Sending an HLR request
```php
$st->hlr()->phone(7912345678)->get(); // 123456789101213
```

Getting the status of an HLR request
```php
$st->hlr()->status(123456789101213);
```

## SMS
Sending an SMS message
```php
$st->sms()->send()
->text('text message')
->to(['+79123456789', 79123456789, '+7(912)-345-67-89'])
->get();
```
Sending messages to multiple recipients with different text.
```php
$phones_array = [
    '+79123456789',
    79123456789,
    '+7(912)-345-67-89',
];

$phones_array2 = [79987654321];

$st->sms()->send()
->validity(10)
->package(static function ($s) use ($phones_array) {
    return $s->text('test')->to($phones_array);
})
->package(static function ($s) use ($phones_array2) {
    return $s->text('test2')->to($phones_array2);
})
->get();
```
Getting the status of an SMS message
```php
$st->sms()->status(14561456165332)->get();
```
Receiving incoming SMS messages
```php
$st->sms()->incoming()->start('2020/01/29 13:00')->end('2020/01/30 13:00')->get();
```


## Email
**Establishing a connection**
```php
$st->setup()->email('password_from_pa');
```


### Working with databases
Getting a list of databases
```php
$st->email()->list()->method('get')
->listId(123)
->get();
```
Adding an address database
```php
$st->email()->list()->method('add')
->name('TestBase')
->abuseEmail('usermail@mail.com')
->abuseName('OwnerName')
->company('CompanyName')
->address('MyAddress')
->city('Spb')
->zip(190000)
->county('Russia')
->url('mysite.com')
->phone(79999999999)
->get();
```
Updating the contact information of the address database
```php
$st->email()->list()->method('update')
->listId(123)
->name('TestBase')
->abuseEmail('usermail@mail.com')
->abuseName('OwnerName')
->company('CompanyName')
->address('MyAddress')
->city('Spb')
->zip(190000)
->county('Russia')
->url('mysite.com')
->phone(79999999999)
->get();
```
Deleting an address database
```php
$st->email()->list()->method('delete')
->listId(123)
->get();
```
Obtaining a list of subscribers with the ability to filter and adjust the issuance
```php
$st->email()->list()->method('get_members')
->listId(123)
->state('active')
->limit(1)
->get();
```
Importing subscribers from a file
```php
$st->email()->list()->method('upload')
->listId(123)
->file('http://www.mysite.ru/files/file.csv')
->type('csv')
->get();
```
Adding one subscriber to the database
```php
$st->email()->list()->method('add_member')
->listId(123)
->email('testuser@mail.com')
->merge(1, 'John')
->merge(2, 'Ivanov')
->merge(3, '1985-11-23')
->gender('m')
->get();
```
Editing subscriber data
```php
$st->email()->list()->method('update_member')
->memberId(123)
->merge(1, 'John')
->merge(2, 'Ivanov')
->merge(3, '1985-11-23')
->gender('m')
->get();
```
Removing a subscriber from the database
```php
$st->email()->list()->method('delete_member')
->memberId(123)
->get();
```
Unsubscribing from the database
```php
$st->email()->list()->method('unsubscribe_member')
->memberId(123)
->listId(123)
->email('testuser@mail.com')
->reason('Request Unsubscribe')
->get();
```



## Viber
**Establishing a connection**
```php
$st->setup()->viber('sourceAddressIM');
```
Send message: _text only_
```php
$st->viber()
->text('Hi viber')
->to(79211234567)
->validity