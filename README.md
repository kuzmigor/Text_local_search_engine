# Text local search engine
Программа для локального текстового поиска по файлам.
## Описание проекта
### Задача
Разработать текстовый поисковый движок по локальным файлам.
Поисковый движок должен быть реализован как консольное приложение (исполняемый файл, запускаемый на сервере), осуществляющее поиск текста в указанных файлах и конфигурируемое через файл.
### Функциональные требования
1. Программа должна запускаться с определяемыми до запуска параметрами: наименование, версия, максимальное количество ответов на один запрос, список файлов для поиска. Также могут быть указаны файл с поисковыми запросами и файл для записи результатов поиска. Если эти файлы не указаны, используются значения по умолчанию.
2. Значения параметров должны быть взяты из файла конфигурации с форматом JSON (см. приложение 1).
3. Поиск должен выполняться по одному и более локальным файлам с форматом TXT.
4. Список локальных файлов должен быть представлен одним из параметров конфигурации.
5. ЛОкальный файл в списке должен иметь вид "./путь/к/файлу/файл.txt".
6. Список поисковых запросов должен передаватьсяв в программу из отдельного файла с форматом JSON (см. приложение 2).
7. Поиск должен выполняться по любому количеству запросов.
8. Результатом успешного поиска для каждого файла должна быть пара из ID документа и рейтинга его релевантности. ID документа это номер документа по порядку в списке локальных файлов, начиная с 0.
9. Рейтинг релевантности должен рассчитываться как сумма слов из запроса, найденных в документе.
10. В случае, когда по запросу найдено более одного файла, результаты поиска должны быть отсортированы от более релевантного файла к менее релевантному, затем файлы с одинаковой релевантностью должны быть отсортированы по ID.
11. Должен рассчитываться рейтинг относительной релевантности по формуле (см. приложение 3).
12. Результаты поиска должны записываться в файл с форматом JSON в виде «ID документа, относительный рейтинг» (см. приложение 4).
13. Максимальное количество возможных документов в ответе на запрос должен определять параметр конфигурационного файла.

##### Примечания
- Содержимое локальных файлов и строк поисковых запросов ограничено латиницей в нижнем регистре, без цифр и знаков пунктуации.

### Нефункциональные требования
1. Алгоритм программы должен обходить все файлы и индексировать их так, чтобы потом по любому поисковому запросу находить наиболее релевантные документы.
2. В программе должны быть реализованы следующие классы.
    - ConverterJSON – класс для работы с JSON-файлами;
    - InvertedIndex – класс для индексации файлов;
    - SearchServer – класс для обработки поисковых запросов.
3. Программа должна успешно проходить требуемые тесты (см. [/tests]).

## Используемые библиотеки
- [STL] – C++ standard template library;
- [JsonCpp] – C++ library that allows manipulating JSON values;
- [GoogleTest] – C++ testing and mocking framework.

## Установка и запуск
__Важно! Работа программы протестирована только под управлением ОС Windows!__
1. Распакуйте архив.
2. Перейдите в директорию проекта, создайте в ней директорию "build".
3. Перейдите в созданную директорию, запустите в ней терминал Windows (Контекстное меню -> «Открыть в терминале»).
4. В терминале выполните команду: ```cmake ..```.
5. Дождитесь завершения выполнения команды. Вы увидите в терминале сообщение: ```-- Build files have been written to: Диск:/Путь/к/папке/build```.
6. В терминале выполните команду: ```--build . --config Release --target all```.
7. Дождитесь завершения выполнения команды.
8. В терминале выполните команду: ```cp -r ..\env\* .\src\```.
9. В терминале выполните команду: ```cd .\src\```.
10. В терминале выполните команду для запуска программы: ```.\search_engine.exe```.
11. Дождитесь завершения работы программы. Вы увидите в терминале сообщение: ```KuzmIgorSearchEngine version 0.1.0 execution completed```.
12. В терминале выполните команду для вывода результата работы программы: ```cat .\answers.json```.

##### Примечания
- Шаг 8 может быть пропущен, тогда файлы конфигурации и запросов необходимо создать вручную.
- Программа использует CLI для вывода результата, но не предполагает взаимодействия с пользователем.
- Текстовые данные должны состоять только из слов, написанных строчными латинскими буквами и разделенных пробелами.
- Для валидации и форматирования полученного вывода может быть использован онлайн-сервис [jsonlint].
- Содержимое папки Resources и файла requests.json служит для примера работы программы. Редактируйте их в соответствии с собственной потребностью.

## Приложения к проекту
### Приложение 1. Требования к конфигурационному файлу
Конфигурационный файл в формате JSON должен иметь следующий вид.
```
{
    "config": {
        "name": "name",
        "version": "version",
        "max_responses": 5
    },
    "files": [
        "./resources/file001.txt",
        "./resources/file002.txt",
        "./resources/file003.txt",
        "./resources/file004.txt",
        …
    ]
}
```
### Приложение 2. Требования к файлу со списком запросов
Файл со списком запросов в формате JSON должен иметь следующий вид.
```
{
    "requests": [
        "some words..",
        "some words..",
        "some words..",
        "some words..",
        …
    ]
}
```
### Приложение 3. Формула расчета относительной релевантности
Относительная релевантность найденного документа должна рассчитываться по следующей формуле.
```
R(отн) = R(абс) / n
```
где
- _R(отн)_ – относительная релевантность;
- _R(абс)_ – максимальная абсолютная релевантность среди найденных документов;
- _n_ – количество найденных документов.

### Приложение 4. Требования к файлу с ответами
Файл со списком ответов в формате JSON должен иметь следующий вид.
```
{
    "answers": {
        "request001": {
            "result": "true",
            "relevance": [
                {
                    "docid": 0,
                    "rank": 0.989
                },
                {
                    "docid": 1,
                    "rank": 0.897
                },
                {
                    "docid": 2,
                    "rank": 0.75
                },
                {
                    "docid": 3,
                    "rank": 0.67
                },
                {
                    "docid": 4,
                    "rank": 0.561
                }
            ]
        },
        "request002": {
            "result": "true",
            "docid": 0,
            "rank": 0.769
        },
        "request003": {
            "result": "false"
        }
    }
}
```

[//]: #

   [JsonCpp]: <https://github.com/open-source-parsers/jsoncpp>
   [STL]: <https://en.cppreference.com/w/>
   [GoogleTest]: <https://github.com/google/googletest>
   [jsonlint]: <https://jsonlint.com/>
   [/tests]: <https://github.com/kuzmigor/Text-local-search-engine/tests>