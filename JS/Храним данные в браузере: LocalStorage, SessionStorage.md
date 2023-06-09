# Хранилища браузера: LocalStorage, SessionStorage

Иногда нам может потребоваться хранить какие-то данные, связанные с пользователем: язык, тему оформления, настройки, предпочтения и между сессиями. И иногда это целесообразней вынести на стороны клиенту, чем писать в базу.

* __`localStorage`__ – объект для хранения в браузере данных связанных с определенным сайтом. Он не очищается при перезагрузке страницы, и даже при закрытии браузера. Используется для хранения данных между сессиями.
* __`sessionStorage`__ – клон прошлого объекта, но он хранит данные только одной вкладки и только пока она открыта. После ее закрытие объект очистится. Используется для хранения данных в рамках сессии.

Все они являются свойствами глобального объекта-window, можно обратиться через него `window.localStorage` или напрямую `sessionStorage`. По структуре схож с JSON-файлом.

<br>

## Особенности:
* Хранит только строки по строковому ключу (если нужен другой, вытаскивая из него данные, нужно приводить тип данных);
* Объекты храняться только в сериализованном формате;
* Максимальный размер харнилища на один ресурс 5,1 мб;
* Для каждого домена браузер создает отдельный объект, редактировать и просматривать его можно только с этого домена;
* Отличная поддержка браузерами и стандартами;

<br>

## Команды:
* Запись (перезапись)  
```javascript
localStorage.setItem(‘ ключ ’, ‘ значение ’);
```
* Получение данных  
```javascript
localStorage.getItem(' ключ ');
```
* Удаление данных / Полное удаление
```javascript
localStorage.removeItem(‘ ключ ‘);
localStorage.clear();
```
