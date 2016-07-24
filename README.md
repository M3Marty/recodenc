<meta charset='utf8'>
<!-- 1. -->

## Предисловие

EU4 официально локализована на английский, немецкий, французский, испанский. Для поддержки этих языков достаточно кодировки CP1252 (висьмибитная кодировка), поддержка юникода в движке игры отсутствует. Соответственно, поддержка кириллицы отсутствует. Для нужд русского перевода игры потребовался механизм отображения кириллицы. Т. к. движок игры поддерживает восьмибитную кодировку, заменив поставляемые для поддержки CP1252 шрифты на шрифты для CP1251, можно отобразить кириллицу, закодированную в CP1251. Эти две кодировки совместимы по кодовым позициям ASCII в начале каждой из кодовых страниц (0-127), что позволяет отображать текст в ASCII, но текст, содержащий символы из второй половины кодировки (128-255) CP1252, отображается как кириллица. Эта проблема малозначительна для Full-версии перевода, но для Lite-версии, где смешан текст в латинице и кириллице, она встаёт в полный рост. Для решения проблемы одновременного отображения расширенной латиницы (вторая половина CP1252) и кириллицы я разработал специальную кодировку «[CP1252+CYR](https://www.dropbox.com/s/1i0eogr3z1xvsms/table_of_encoding.ods?dl=1)», совместимую с CP1252 по расположению латиницы, а специальные типографские символы (напр., ¥) заменил на буквы кириллицы, отсутствующие в литинице. Однако, у кодировки CP1251 есть серьёзное преимущество — сортировка по алфавиту.

<!-- 2. -->

## Файлы локализаций

Официальная локализация поставляется в виде файлов YAML (`*.yml`) в кодировке UTF-8 с BOM, обрыв строки LF (в формате \*nix). Но движок игры использует CP1252, поэтому на этапе загрузки локализации происходит перекодировка: символы с позиций в кодировке юникод становятся на позиции в кодировке CP1252. Следовательно, чтобы запросить отображение кириллического символа, его код после перекодировки должен соответствовать номеру в шрифте. Поэтому кириллицу, закодированную в UTF-8 на этапе редактирования перевода, нужно заменить на символы, которые после перекодирования движком игры отобразятся как кириллица. В этом нам поможет скрипт Recodenc.

Все остальные файлы поставляются в кодировке CP1252, обрывы строк CRLF. Редактируются они в кодировке CP1251 при помощи стандартного программного обеспечения (т. к. нужны только для Full-перевода с кодировкой CP1251).

<!--
Файлы локализаций находятся в каталоге /localisation/. Кодировка UTF-8 с BOM, обрывы строк LF. Для работы с этими файлами предназначен перекодировщик.
-->

<!-- 3. -->

## Шрифты для отображения

Движок игры сопоставляет кодовую позицию символа с его рисунком в шрифте и выводит его. Существует несколько программ для рендеринга шрифтов, совместимых с движком игры, я использовал [BMFont](http://angelcode.com/products/bmfont/). Сгенерированный шрифт располагается в двух файлах: изображении `*.tga` или `*.dds` и текстовом файле `*.fnt`, содержащим карту изображения и связывающим кодовую позицию символа с его представлением в графическом виде. Сгенерировать растровые шрифты для кодировки CP1251 относительно легко: достаточно указать программе эту кодировку и исходный векторный шрифт. Для создания шрифта для кодировки CP1252+CYR необходимо выбрать кодировку юникод и в ней [выбрать символы из CP1252+CYR](https://www.dropbox.com/s/sni9bomyngpl5sk/bmf.7z?dl=1), отрендерить шрифт, и в fnt-файле в столбце «id» и секции кернингов заменить номера кодовых позиций юникода на номера из CP1252+CYR. Это можно сделать при помощи скрипта Recodenc.

### Предлагаемые шрифты

[CP1252+CYR](https://www.dropbox.com/s/bch2ar6xtpoc0m4/fonts_cp1252%2Bcyr_v1.7z?dl=1)

Шрифты предлагаются только для кодировки CP1252+CYR, т. к. выбранный шрифт поддерживает только юникод. Чтобы воспользоваться ими, распакуйте скачанный архив в /gfx/fonts/.

<!-- 4. -->

## Раскладка клавиатуры для ввода

Движок игры при вводе с клавиатуры ожидает получить *байтовые* коды символов, которые от выводит в текстовом поле и обрабатывает. Поэтому, когда вводятся символы за пределами однобайтового начала кодировки юникод, windows передаёт коды символов в кодировке UTF-16, что движок игры воспринимает как два отдельных символа и соответственно выводит их в текстовом поле. Для передачи правильных кодов символов необходимы специальные раскладки клавиатуры.

### Предлагаемые раскладки

[CP1251](https://www.dropbox.com/s/54p4dbwtjk3l8he/eu4-ru-cp1251.7z?dl=1)

[CP1252+CYR](https://www.dropbox.com/s/p4pmsazofqa5v5m/eu4-ru-cp1252%2Bcyr.7z?dl=1)

Скачайте по указанным выше ссылкам архивы. В них находятся файлы установщика и исходники раскладок для программы Microsoft Kyeboard Layout Creator. Установите нужную вам раскладку клавиатуры или сразу все, распаковав архивы запустив в распакованных каталогах setup.exe

ПКМ по языковой панели → Параметры... → Переключение клавиатуры → для пункта «Переключить языки ввода» нажмите кнопку «Сменить сочетание клавиш» и в появившемся окне настройте *разные* комбинации клавиш для переключения языков ввода и смены раскладки клавиатуры. Это нужно для того, чтобы пользоваться, например, парой раскладок «стандартная RU — стандартная EN» пока вы работаете в обычных программах, переключить раскладку на языке RU, и использовать пару раскладок «RU Layout for EUIV CP1252+CYR — стандартная EN», пока вы играете в EU4.

На вкладке «Общие» проверьте наличие клавиатурных раскладок в секции русского языка, и, при необходимости, измените порядок.

Общее:

* Кавычка (`"`) не работает, поэтому она заменена на одинарную (`'`).
* Знак номера заменён на решётку, т. к. он отсутствует в кодировке CP1252.
* Букву «Ёё» вводить через AltGr(правый Alt)+русская Ее

CP1251:

* CapsLock не работает на буквах: ЯяЧчЁё

CP1252+CYR:

* CapsLock работает на буквах: ЕеХхАаРрОоСс

<!-- Для передачи правильных кодов символов я подготовил две раскладки для кодировок CP1251 и CP1252+CYR. -->

<!-- 5. -->

## Как пользоваться скриптом

Перекодировщик предназначен для промежуточного преобразования кодовых позиций символов кириллицы в кодировке UTF8 из их расположения в кодировке UTF8 к позициям символов, которые после перекодировки движком соответственно становятся на позиции символов кириллицы в кодировках CP1251 и CP1252+CYR. Данный перекодировщик отличается от других тем, что содержит официальную реализацию кодировки CP1252+CYR.

Для того, чтобы воспользоваться скриптом, установите Perl не ниже версии 5.18 и модуль Tk, а также скачайте этот проект.

### EU4

Форма «EU4» содержит органы управления для перекодировки файлов локализаций из/для /localisation/.

Сначала выберите кодировку. Если вы собираетесь декодировать файлы, то выбранная кодировка сообщает скрипту кодироку исходных данных, если же вы собираетесь кодировать файлы, скрипт таким образом узнает желаемую кодировку выходных данных. Обратите внимание, что декодировать можно только CP1251, т. к. только в этой кодировке из представленных латиница не пересекается с кириллицей, и, декодировав её, можно получить идентичные оригиналу файлы. Транслит предназначен для тех, кто хочет играть с переводом и одновременно использовать красивые латинские шрифты из модов.

Для указания скрипту каталога с исходными данными введите *абсолютный* путь в текстовое поле самостоятельно или вызовите диалог выбора каталога кнопкой справа от текстового поля. Формат входных файлов: кодировка UTF-8 с BOM, обрыв строки LF (в стиле \*nix). Если вы не хотите изменять исходные файлы, отметьте галочкой пункт «Сохранить в» и выберите каталог для сохранения выходных данных. Каталог сохранения не очищается перед записью — файлы с именами, прочитанными из исходного каталога, будут перезаписаны в каталоге сохранения. Структура файлов будет воспроизведена в выходном каталоге. Формат выходных файлов: кодировка UTF-8 с BOM, обрыв строки LF (в стиле \*nix). Открывать закодированные файлы при помощи стандартного программного обеспечения не рекомендуется (особенно встроенным в windows Блокнотом). Обратите внимание, что отдельные файлы не поддерживаются, как и вложенные каталоги.

Для перекодировки нажмите на кнопку с желаемым действием и дождитесь появления в строке состояния (слева от кнопки «Закрыть» внизу окна) надписи «Готово!» или сообщения об ошибке.

Если в строке состояния появилось сообщение об ошибке, значит не удаётся прочитать/записать в каталог, возможно, он не существует, или у вас нет парава на чтение/запись в него.

### EU4 шрифт

Форма «EU4 шрифт» содержит органы управления для замены в fnt-картах растрового шрифта в столбце «id» и секции кернингов номеров кодовых позиций юникода на номера из CP1252+CYR.

Выберите каталоги как сказано выше. Формат входных файлов: кодировка ASCII, обрыв строки 	CRLF (в стиле dos). Формат выходных файлов такой же.

Нажмите кнопку «Кодировать» и дождитесь сообщения в статусной строке о завершении операции или ошибке.

<!-- 6. -->

## Файлы в проекте и их функции

`LICENSE` — лицензия, под которой доступны файлы проекта.

`README.md` — этот файл.

`recodenc.pl` — скрипт-перекодировщик.

`script.txt` — файл, в котором записана справочная информация о кодировках.

<!-- 7. -->

## Кодировки

Символы кодировки CP1252 (отображаемые EU4):

```
 !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
€‚ƒ„…†‡ˆ‰Š‹ŒŽ‘’“”•–—˜™š›œžŸ ¡¢¥¦¨©ª«¬®¯°±²³´µ¶·¸¹º»¼¾¿ÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏÐÑÒÓÔÕÖ×ØÙÚÛÜÝÞßàáâãäåæçèéêëìíîïðñòóôõö÷øùúûüýþÿ
```

Символы кодировки CP1251 (отображаемые EU4; без ASCII):

```
Ђ‚ѓ„…†‡€‰Љ‹ЊЋЏ‘’“”•–—™љ›њћџ ЎўҐ¦Ё©Є«¬®Ї°±Ііґµ¶·ё№є»јѕїАБВГДЕЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯабвгдежзийклмнопрстуфхцчшщъыьэюя
```

Символы кодировки CP1252+CYR (отображаемые EU4; без ASCII):

```
БГДЖЗИЙЛПŠУŒŽФЦЧШЩЪЫЬЭšЮœžŸб¡вгджзийклмнптуфцчшщъыьэю¿ÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏÐÑÒÓÔÕÖЯØÙÚÛÜÝÞßàáâãäåæçèéêëìíîïðñòóôõöяøùúûüýþÿ
```

<!-- 8. -->

## Примеры кодированного текста

Пример взят из `/localisation/aow*.yml`.

Оригинал:

```
desc_autonomy_increased:0 "Этим провинциям была дарована высокая автономия, благодаря этому беспорядки снижены."
```

Кодировано в CP1251:

```
desc_autonomy_increased:0 "Ýòèì ïðîâèíöèÿì áûëà äàðîâàíà âûñîêàÿ àâòîíîìèÿ, áëàãîäàðÿ ýòîìó áåñïîðÿäêè ñíèæåíû."
```

Кодировано в CP1252+CYR:

```
desc_autonomy_increased:0 "™²ª¯ ±po¢ª°µª÷¯  º®a ¦apo¢a°a ¢ºco¬a÷ a¢²o°o¯ª÷,  ®a¥o¦ap÷ ¼²o¯³  ec±op÷¦¬ª c°ª¨e°º."
```

Транслитерировано:

```
desc_autonomy_increased:0 "Êtim provinqiäm bîla darovana vîsokaä avtonomiä, blagodarä êtomu besporädki snijenî."
```
