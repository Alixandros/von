--1--
import requests
import json
import base64


def get_user_repo_info(username):
    """
    Получает информацию о репозитории пользователя и README файл
    """
    # URL для получения информации о пользователе
    user_url = f"https://api.github.com/users/{username}"

    try:
        # Получаем информацию о пользователе
        user_response = requests.get(user_url)

        # Если пользователь не найден (статус 404)
        if user_response.status_code == 404:
            return None, "Пользователь не найден", None

        user_response.raise_for_status()
        user_data = user_response.json()

        # Для отладки: посмотреть все поля пользователя
        print("\n📋 ВСЯ информация о пользователе:")
        print("=" * 50)
        for key, value in user_data.items():
            print(f"{key}: {value}")
        print("=" * 50)

                def print_all_user_info(user_data):
            """
            Выводит всю доступную информацию о пользователе
            """
            print("\n📋 Полная информация о пользователе:")
            print("=" * 60)

            important_fields = [
                'login', 'name', 'bio', 'location', 'company',
                'blog', 'email', 'twitter_username',
                'public_repos', 'followers', 'following',
                'created_at', 'updated_at'
            ]

            for field in important_fields:
                value = user_data.get(field, 'Не указано')
                print(f"• {field}: {value}")

            print("=" * 60)

        # Получаем список репозиториев пользователя
        repos_url = user_data['repos_url']
        repos_response = requests.get(repos_url)
        repos_response.raise_for_status()
        repos_data = repos_response.json()

        # Ищем репозиторий с именем пользователя (обычно это основной репозиторий)
        personal_repo = None
        for repo in repos_data:
            if repo['name'].lower() == username.lower():
                personal_repo = repo
                break

        # Если не нашли репозиторий с именем пользователя, берем первый репозиторий
        if not personal_repo and repos_data:
            personal_repo = repos_data[0]

        if not personal_repo:
            return None, "У пользователя нет репозиториев", None

        # Получаем README файл
        readme_url = f"https://api.github.com/repos/{username}/{personal_repo['name']}/readme"
        readme_response = requests.get(readme_url)

        if readme_response.status_code == 200:
            readme_data = readme_response.json()
            # Декодируем содержимое README из base64
            readme_content = base64.b64decode(readme_data['content']).decode('utf-8')
        else:
            readme_content = "README файл не найден"

        return user_data, readme_content, personal_repo['name']

    except requests.exceptions.RequestException as e:
        return None, f"Ошибка при подключении к GitHub: {e}", None
    except json.JSONDecodeError:
        return None, "Ошибка при обработке данных", None


def process_user(username):
    """
    Обрабатывает запрос для одного пользователя
    """
    print(f"\n🔍 Ищем пользователя {username} на GitHub...")

    # Получаем информацию о пользователе и его README
    user_data, readme_content, repo_name = get_user_repo_info(username)

    if user_data is None:
        if readme_content == "Пользователь не найден":
            print(f"👋 Здравствуйте, неизвестный пользователь!")
        else:
            print(f"❌ {readme_content}")
        return #Напишит что приветствие неизвестного пользователя и попросит вести имя/никнейм еще раз

  print(f"👤 Логин: {user_data['login']}")
print(f"📛 Приветствуем: {user_data.get('name', 'Не указано')}")
print(f"📝 Био: {user_data.get('bio', 'Не указано')}")
print(f"📍 Локация: {user_data.get('location', 'Не указано')}")

# Соцсети и контакты
print(f"🏢 Компания: {user_data.get('company', 'Не указано')}")
print(f"🌐 Сайт: {user_data.get('blog', 'Не указано')}")
print(f"📧 Email: {user_data.get('email', 'Не указано')}")
print(f"🐦 Twitter: {user_data.get('twitter_username', 'Не указано')}")

# Статистика
print(f"📂 Репозитории: {user_data['public_repos']}")
print(f"👥 Подписчики: {user_data['followers']}")
print(f"👤 Подписки: {user_data['following']}")
print(f"📅 Создан: {user_data['created_at']}")

    # Выводим содержимое README
    print("\n📖 Содержимое README файла:") 
    print("-" * 30)
    if readme_content == "README файл не найден":
        print("❌ README файл не найден в репозитории")
    else:
        # Ограничиваем вывод до первых 500 символов
        preview = readme_content[:500] + "..." if len(readme_content) > 500 else readme_content
        print(preview)
        if len(readme_content) > 500:
            print("... (показаны первые 500 символов)")

    print("\n" + "=" * )
    print("✨ Приятного кодирования!")


def main():
    """
    Основная функция программы
    """
    print("👋 Добро пожаловать в GitHub приветственную программу!") #Программа приветствует
    print("=" * 50)
    print("Введите 'exit' или 'quit' для выхода из программы") #Надо ввести 'exit' или 'quit' для выхода/окончания программы
    print("=" * 50)

    while True:
        # Получаем имя пользователя
        username = input("\nВведите имя пользователя GitHub: ").strip() #Надо весть имя/никнейм пользователя которое он зарегестрировался в github.com

        # Проверяем команды выхода
        if username.lower() in ['exit', 'quit', 'выход']:
            print("👋 До свидания!") #'exit', 'quit', 'выход' если их вести введя/ не введя имя/никнейм пользователь
            break

        if not username:
            print("❌ Имя пользователя не может быть пустым!") #Если нажать пробел в "Введите имя пользователя GitHub:" то выведется "❌ Имя пользователя не может быть пустым!"
            continue

        # Обрабатываем пользователя
        process_user(username)


if __name__ == "__main__":
    main()
--2--
def get_user_repo_info(username):
    """
    Получает информацию о репозитории пользователя и README файл
    """
    # URL для получения информации о пользователе
    user_url = f"https://api.github.com/users/{username}"

Что делает:
Формирует URL для запроса к GitHub API;
Получает данные пользователя;
Проверяет статус ответа (404 если пользователь не найден);
Получает список репозиториев пользователя;
Ищет репозиторий с именем, совпадающим с именем пользователя;
Если не находит - берет первый репозиторий;
Получает и декодирует README файл из base64;
Обрабатывает возможные ошибки соединения.
--3--
def process_user(username):
    """
    Обрабатывает запрос для одного пользователя
    """
    print(f"\n🔍 Ищем пользователя {username} на GitHub...")

    Что делает:
Выводит сообщение о начале поиска;
Вызывает get_user_repo_info() для получения данных;
Обрабатывает случаи, когда пользователь не найден;
Форматирует и выводит информацию о пользователе;
Отображает содержимое README (первые 500 символов);
Использует эмодзи и разделители для красивого оформления.
--4--
Назначение: Основной цикл программы

def main():
    """
    Основная функция программы
    """
    print("👋 Добро пожаловать в GitHub приветственную программу!")
    
Что делает:
Выводит приветственное сообщение;
Создает бесконечный цикл для ввода имен пользователей;
Обрабатывает команды выхода (exit, quit, выход);
Проверяет пустой ввод;
Вызывает process_user() для каждого введенного имени.
--5--
if __name__ == "__main__":
 main()
 
Что делает:
Обеспечивает, что программа запустится только при прямом вызове файла, а не при импорте как модуля.
--6--
Пользовательский опыт:
Интерактивный интерфейс: Постоянный цикл запросов;
Предпросмотр README: Ограничение в 500 символов; это значит что покажется первые 500 символов в README даже если их больше 500
Почему это сделано?
1. Удобство чтения
README файлы могут быть очень длинными (несколько тысяч символов)
Показывать все содержимое в консоли неудобно
Пользователь быстро теряет интерес при большом объеме текста
2. Эффективность
Быстрая загрузка и отображение
Не перегружает консоль
Пользователь сразу видит суть
3. Стимулирование к действию
Если пользователю интересно, он может:
Перейти по ссылке на GitHub
Прочитать полный README в браузере
Увидеть красивую разметку (которая в консоли не отображается)
Что теряется при таком подходе?
1. Полнота информации
Пользователь не видит:
Полные инструкции по установке
Детальное описание функционала
Примеры использования
Лицензионную информацию
2. Форматирование
В консоли не отображается:
Картинки
Таблицы
Ссылки
HTML-разметка
Специальное форматирование;
Команды выхода: Несколько вариантов для завершения работы.
То есть тут есть более 2 вариантов как может кончится программа.
--7--
Ограничения:
Не обрабатывает пагинацию репозиториев (только первые 30):
repos_response = requests.get(repos_url)
repos_data = repos_response.json()  # ← Здесь только первые 30 репозиториев!
Например, если у пользователя 45 репозиториев:
Программа получит только репозитории 1-30
Репозитории 31-45 будут проигнорированы
Если нужный репозиторий был на 35-й позиции — программа его не найдет;
Не кэширует результаты запросов:
Кэширование — это сохранение результатов запросов во временное хранилище (кэш), чтобы при повторном запросе тех же данных не обращаться к серверу снова.
Почему это проблема?
1. Ограничения GitHub API
GitHub имеет лимиты на количество запросов:
60 запросов в час для неаутентифицированных запросов
5000 запросов в час для аутентифицированных
Без кэширования вы быстро исчерпаете лимит!
2. Медленная работа
Каждый запрос к API занимает время:
Сетевая задержка
Обработка на стороне GitHub
Передача данных по сети
3. Избыточная нагрузка
Запрашивать одни и те же данные многократно — неэффективно и создает ненужную нагрузку на серверы GitHub;
Нет обработки частых запросов (может упереться в лимиты GitHub API):
Лимиты API — это ограничения на количество запросов, которые можно сделать к API за определенный промежуток времени.
Для GitHub API:
Без авторизации: 60 запросов в час
С авторизацией: 5000 запросов в час
Что значит "упереться в лимиты"?
Это ситуация, когда ваша программа превышает разрешенное количество запросов, и GitHub начинает блокировать дальнейшие запросы.
--8--
Библиотеки:
1.import requests - для HTTP-запросов:
Что это? Библиотека для простой и elegant работы с HTTP-запросами.
Ключевые возможности:
requests.get() - GET-запросы (получение данных)
requests.post() - POST-запросы (отправка данных)
requests.put() - PUT-запросы (обновление данных)
requests.delete() - DELETE-запросы (удаление данных)
Альтернатива без requests: Пришлось бы использовать сложную встроенную библиотеку urllib;
2.import json - для работы с JSON-данными:
Что это - Встроенная библиотека для работы с JSON (JavaScript Object Notation).
Ключевые функции:
json.loads() - из строки в Python-объект
json.dumps() - из Python-объекта в строку
json.load() - из файла в Python-объект
json.dump() - из Python-объекта в файл
Зачем нужна - GitHub API возвращает данные в формате JSON, который нужно преобразовать в понятные Python-структуры;
3.import base64 - для декодирования base64:
Что это - Встроенная библиотека для работы с base64-кодированием.
Почему это нужно - GitHub хранит содержимое файлов в base64 по нескольким причинам:
Безопасная передача бинарных данных через JSON
Избежание проблем с кодировками
Универсальность формата.
Как все вместе они работает в этой программе:
requests получает сырые данные от GitHub API
json преобразует JSON-ответ в Python-словари и списки
base64 декодирует содержимое README файла
Результат - понятные данные для пользователя.
Почему именно эти библиотеки?
requests - де-факто стандарт для HTTP в Python
json - встроенная, надежная, быстрая
base64 - встроенная, идеально подходит для задачи.
--9--
Функция get_user_repo_info(username)
Назначение: Получить данные о пользователе и его репозитории.
Формирование URL
user_url = f"https://api.github.com/users/{username}"
Запрос данных пользователя
user_response = requests.get(user_url)
if user_response.status_code == 404:  # Если пользователь не существует
    return None, "Пользователь не найден", None
Обработка данных пользователя
user_data = user_response.json()  # Преобразуем JSON в словарь Python
repos_url = user_data['repos_url']  # Получаем URL репозиториев
Получение репозиториев
repos_response = requests.get(repos_url)
repos_data = repos_response.json()  # Список всех репозиториев
оиск нужного репозитория
python
# Ищем репозиторий с именем как у пользователя
for repo in repos_data:
    if repo['name'].lower() == username.lower():
        personal_repo = repo
        break

# Если не нашли - берем первый репозиторий
if not personal_repo and repos_data:
    personal_repo = repos_data[0]
Получение README
python
readme_url = f"https://api.github.com/repos/{username}/{personal_repo['name']}/readme"
readme_response = requests.get(readme_url)

if readme_response.status_code == 200:
    readme_data = readme_response.json()
    # Декодируем из base64 в обычный текст
    readme_content = base64.b64decode(readme_data['content']).decode('utf-8')
else:
    readme_content = "README файл не найден"
Возврат результатов
python
return user_data, readme_content, personal_repo['name']
Функция process_user(username)
Назначение: Обработать и отобразить информацию о пользователе.
Поиск пользователя
print(f"\n🔍 Ищем пользователя {username} на GitHub...")
user_data, readme_content, repo_name = get_user_repo_info(username)
Обработка ошибок
if user_data is None:
    if readme_content == "Пользователь не найден":
        print(f"👋 Здравствуйте, неизвестный пользователь!")
    else:
        print(f"❌ {readme_content}")
    return  # Завершаем функцию
Вывод информации о пользователе
print("\n" + "=" * 50)
print(f"🎉 Приветствуем, {user_data.get('name', username)}!")  # Имя или логин
print(f"📝 Биография: {user_data.get('bio', 'Не указана')}")   # Биография или заглушка
print(f"📍 Местоположение: {user_data.get('location', 'Не указано')}")
print(f"🔗 Профиль: {user_data['html_url']}")  # Ссылка на профиль
print(f"📂 Репозиторий: {repo_name}")  # Название репозитория
print("=" * 50)
Вывод README
print("\n📖 Содержимое README файла:")
print("-" * 30)
if readme_content == "README файл не найден":
    print("❌ README файл не найден в репозитории")
else:
    # Показываем только первые 500 символов
    preview = readme_content[:500] + "..." if len(readme_content) > 500 else readme_content
    print(preview)
    if len(readme_content) > 500:
        print("... (показаны первые 500 символов)")
Функция main()
Назначение: Основной цикл программы.
Приветствие
print("👋 Добро пожаловать в GitHub приветственную программу!")
print("=" * 50)
print("Введите 'exit' или 'quit' для выхода из программы")
 Бесконечный цикл
while True:
    username = input("\nВведите имя пользователя GitHub: ").strip()
Проверка команд выхода
if username.lower() in ['exit', 'quit', 'выход']:
    print("👋 До свидания!")
    break  # Выход из цикла
Проверка пустого ввода
if not username:
    print("❌ Имя пользователя не может быть пустым!")
    continue  # Переход к следующей итерации
Обработка пользователя
process_user(username)  # Вызов основной функции обработки
Запуск программы
python
if __name__ == "__main__":
    main()  # Запуск только если файл выполняется напрямую
Полный поток выполнения:
Запуск → main()
Ввод username → проверка команд выхода
Вызов → process_user(username)
Вызов → get_user_repo_info(username)
Запросы к API → обработка данных → возврат результатов
Вывод информации → возврат в главный цикл
Повтор → до команды выхода
Особенности кода:
Обработка ошибок: try/except для сетевых ошибок
Заглушки значений: .get() с значениями по умолчанию
Валидация: проверка статус-кодов HTTP
Юзер-френдли: эмодзи и красивое форматирование
Безопасность: обработка исключений JSON.
user_data['name'] - полное имя
user_data['bio'] - биография
user_data['location'] - местоположение
user_data['html_url'] - ссылка на профиль
user_data['repos_url'] - ссылка на репозитории
В переменной user_data (основная информация)
user_data = user_response.json()
В переменной readme_content (README файл)
readme_content = base64.b64decode(readme_data['content']).decode('utf-8')
Содержит текст README.md из репозитория пользователя
В переменной repos_data (все репозитории)
repos_data = repos_response.json()
—-10--
Полный порядок элементов после def:
Имя функции - get_user_repo_info
Параметры в скобках - (username)
Двоеточие - :
Docstring (многострочный комментарий) - """Описание функции"""
Тело функции (с отступом 4 пробела)
Возврат значения (опционально) - return
if условие:  # ← Заголовок условия
    # Тело условия (с отступом 4 пробела)
    выполнить_код()
if условие:
    выполнить_если_True()
else:  # ← Без условия!
    выполнить_если_False()
for элемент in последовательность:  # ← Заголовок цикла
    # Тело цикла (с отступом 4 пробела)
    выполнить_код(элемент)
while условие:  # ← Заголовок цикла
    # Тело цикла (с отступом 4 пробела)
    выполнить_код()
 return user_data, readme_content, personal_repo['name']  # ← Возврат 3 значений
Что такое try-except:
try-except - это механизм обработки исключений (ошибок):
try - пробуем выполнить код, который может вызвать ошибку
except - обрабатываем ошибку, если она произошла
else (опционально) - выполняется если ошибок не было
finally (опционально) - выполняется всегда 
 Почему try-except важен в вашем коде:
Сетевые запросы могут fail (нет интернета, GitHub недоступен)
API может вернуть невалидный JSON
Пользователь может не существовать (статус 404)
Предотвращает аварийное завершение программы
Что такое if __name__ == "__main__"::
Это специальная конструкция, которая определяет:
Запущен ли файл напрямую (как основная программа)
Или импортирован как модуль в другой файл
# Без if __name__ == "__main__":
# При импорте модуля код бы выполнился сразу!
# С if __name__ == "__main__":
# Код выполняется только при прямом запуске
if __name__ == "__main__":
    # Это "входная дверь" в вашу программу
    main()
Что такое continue:
continue - это ключевое слово, которое:
Пропускает текущую итерацию цикла
Переходит к следующей итерации сразу
Не прерывает цикл полностью (в отличие от break)
Импорт библиотек
import requests      # для HTTP-запросов к GitHub API
import json         # для работы с JSON-данными
import base64       # для декодирования base64 (README в base64)
Запрос данных пользователя
user_url = f"https://api.github.com/users/{username}"
user_response = requests.get(user_url)
Формирует URL для запроса информации о пользователе
Отправляет GET-запрос к GitHub API
if user_response.status_code == 404:
    return None, "Пользователь не найден", None
Проверяет, существует ли пользователь (статус 404 = не найден)
Получение данных пользователя
user_response.raise_for_status()  # Проверяет ошибки HTTP
user_data = user_response.json()  # Парсит JSON в словарь
raise_for_status() - выбрасывает исключение при ошибке HTTP
.json() - преобразует ответ в Python-словарь
Отладочная информация ⚠️ (проблемное место)
python
def print_all_user_info(user_data):
    # ... функция внутри функции ...
Проблема: Функция определена внутри другой функции, но никогда не вызывается
Решение: Нужно вызвать print_all_user_info(user_data) после определения
Получение репозиториев
repos_url = user_data['repos_url']  # URL из данных пользователя
repos_response = requests.get(repos_url)
repos_data = repos_response.json()  # Список репозиториев
оиск репозитория
python
for repo in repos_data:
    if repo['name'].lower() == username.lower():
        personal_repo = repo
        break
Ищет репозиторий с именем, совпадающим с именем пользователя
break - прерывает цикл при нахождении
Резервный выбор репозитория
if not personal_repo and repos_data:
    personal_repo = repos_data[0]  # Берет первый репозиторий
Если репозитория с именем пользователя нет, берет первый из списка
Получение README
readme_url = f"https://api.github.com/repos/{username}/{personal_repo['name']}/readme"
readme_response = requests.get(readme_url)
if readme_response.status_code == 200:
    readme_data = readme_response.json()
    readme_content = base64.b64decode(readme_data['content']).decode('utf-8')
GitHub возвращает README в base64, поэтому нужно декодировать
.decode('utf-8') - преобразует байты в строку
Обработка ошибок
python
except requests.exceptions.RequestException as e:
    return None, f"Ошибка при подключении к GitHub: {e}", None
except json.JSONDecodeError:
    return None, "Ошибка при обработке данных", None
Обрабатывает сетевые ошибки и ошибки парсинга JSON
Получение данных
user_data, readme_content, repo_name = get_user_repo_info(username)
Получает кортеж из трех значений от предыдущей функции
Проверка ошибок
if user_data is None:
    if readme_content == "Пользователь не найден":
        print(f"👋 Здравствуйте, неизвестный пользователь!")
Обрабатывает случай, когда пользователь не найден
Вывод информации
print(f"👤 Логин: {user_data['login']}")
print(f"📛 Приветствуем: {user_data.get('name', 'Не указано')}")
# ... и так далее ...
.get('key', 'default') - безопасное получение значения с default
Выводит основную информацию о пользователе
Вывод README
preview = readme_content[:500] + "..." if len(readme_content) > 500 else readme_content
Ограничивает вывод README до 500 символов
Бесконечный цикл
while True:
    username = input("\nВведите имя пользователя GitHub: ").strip()
strip() - удаляет пробелы в начале и конце
Проверка выхода
if username.lower() in ['exit', 'quit', 'выход']:
    print("👋 До свидания!")
    break  # Выход из цикла
break - прерывает цикл while
Проверка пустого ввода
if not username:
    print("❌ Имя пользователя не может быть пустым!")
    continue  # Переход к следующей итерации
continue - пропускает текущую итерацию цикла
Обработка пользователя
process_user(username)  # Вызов функции обработки
Запуск программы
if __name__ == "__main__":
    main()
Запускает main() только если файл выполняется напрямую
Не выполняется при импорте модуля
print("\n📋 Полная информация о пользователе:")
            print("=" * 60)

