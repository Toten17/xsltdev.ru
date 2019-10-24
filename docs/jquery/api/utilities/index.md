# Утилиты

В этом разделе представлено описание различных функций (утилит) JavaScript библиотеки jQuery для работы с элементами DOM, HTML и XML строками, массивами, объектами, функциями и так далее.

`$.contains()`
: Позволяет проверить и возвратить логическое значение, которое определяет является ли элемент DOM потомком другого элемента DOM.

`$.data()`
: Позволяет сохранить произвольные данные, связанные с указанным элементом, и/или возвращать заданное значение.

\$.each()
: Универсальная функция итератор, которая может использоваться для перебора элементов объекта, так и массива, применяя функцию обратного вызова к каждому элементу массива, или объекта.

`$.extend()`
: Позволяет объединить содержимое двух, или более объектов в один объект.

`$.fn.extend()`
: Позволяет объединить содержимое объекта с прототипом jQuery, чтобы предоставить объекту методы экземпляра jQuery.

`$.globalEval()`
: Позволяет выполнить определенный код JavaScript в глобальной области видимости.

`$.grep()`
: Позволяет найти элементы массива, которые удовлетворяют фильтрующей функции, исходный массив при этом не изменяется.

`$.inArray()`
: Производит поиск переданного значения в массиве и возвращает его индекс при нахождении, или значение `-1`, если не найден.

`$.isArray()`
: Определяет, является ли переданное значение в параметре метода массивом JavaScript.

`$.isEmptyObject()`
: Позволяет проверить является ли переданный объект пустым (не содержит перечислимых свойств - атрибут `enumerable` которых, имеет значение `true`).

`$.isNumeric()`
: Определяет, является ли переданное значение в параметре метода числом JavaScript.

`$.isPlainObject()`
: Позволяет проверить является ли объект простым объектом (созданным с помощью `{}`, или `new Object`).

`$.isXMLDoc()`
: Позволяет проверить находится ли узел DOM внутри XML документа, или является XML документом.

`$.makeArray()`
: Позволяет преобразовать массивоподобный объект в JavaScript массив.

`$.map()`
: Позволяет перевести все элементы массива, или объекта в новый массив элементов, применяя функцию обратного вызова к каждому элементу массива, или объекта.

`$.merge()`
: Позволяет объединить содержимое двух массивов в один массив.

`$.noop()`
: Определяет пустую функцию.

`$.now()`
: Возвращает число, представляющее текущее время.

`$.parseHTML()`
: Позволяет преобразовать переданную HTML строку в массив DOM узлов.

`$.parseXML()`
: Позволяет преобразовать переданную XML строку в XML документ.

`$.removeData()`
: Позволяет удалить значения, которые были установлены с помощью метода `$.data()`.

`$.trim()`
: Позволяет удалить пробелы из начала и конца строки.

`$.type()`
: Позволяет определить внутренний JavaScript класс объекта (`[[Class]]`).

`$.uniqueSort()`
: Производит сортировку массива элементов DOM с удалением дубликатов.