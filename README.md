

```markdown
# Hello Python CI/CD Pipeline

Пример минималистичного приложения на **Python**, демонстрирующий настройку **пайплайна CI/CD** через GitHub Actions и публикацию оптимизированных **бинарников** в GitHub Releases с помощью **PyInstaller**.

## 🚀 О проекте

Этот проект демонстрирует преимущества использования Python + PyInstaller для создания самодостаточных исполняемых файлов:
- **Упаковка интерпретатора:** PyInstaller встраивает Python Runtime и все зависимости прямо в бинарник.
- **Single File:** Всё упаковано в один исполняемый файл (~8 MB), пользователю не нужен установленный Python.
- **Автоматизация:** При создании тега `v*` запускается матричная сборка на 3 ОС, и все бинарники автоматически прикрепляются к новому релизу.

> ⚠️ **Важно:** В отличие от Go/Rust, Python **не поддерживает cross-compilation**. PyInstaller встраивает платформо-зависимый интерпретатор, поэтому для каждой ОС (Linux, macOS, Windows) нужен **свой runner** в CI.

## 📂 Структура проекта

```text
hello-python/
├── .github/workflows/
│   └── ci.yml              # Конфигурация CI (GitHub Actions)
├── hello/
│   ├── __init__.py         # Версия пакета
│   └── greeting.py         # Пакет с функциями
├── tests/
│   └── test_greeting.py    # Юнит-тесты (pytest)
├── .gitignore
├── main.py                 # Точка входа
├── pyproject.toml          # Конфигурация проекта и версия
├── requirements.txt        # Зависимости
── README.md
```

## 🛠 Локальный запуск

### Вариант 1: Локально (через Docker)
Не требует установки Python. Просто нужен Docker:

```bash
# Запуск тестов
docker run --rm \
  -v "${PWD}:/app" \
  -w /app \
  python:3.12-slim \
  sh -c "pip install -r requirements.txt && python -m pytest -v"

# Сборка бинарника через PyInstaller
docker run --rm \
  -v "${PWD}:/app" \
  -w /app \
  python:3.12 \
  sh -c "pip install -r requirements.txt && \
         python -m PyInstaller --onefile --name hello-python main.py && \
         ls -la dist/"

# Проверка собранного бинарника в чистом контейнере
docker run --rm \
  -v "${PWD}/dist":/dist \
  debian:stable-slim \
  /dist/hello-python
```

**Ожидаемый результат:**
```text
hello-python version 0.1.0
Hello from Python! 📦
OS: linux
Arch: x86_64
Hello, GitHub!
Sum 1..10 = 55
```
![загрузка](/images/777.jpg)

### Вариант 2: Скачивание готового бинарника
Не требует установки Python или Docker. Просто скачайте файл из раздела 

**Windows (PowerShell):**
```powershell
Invoke-WebRequest 
.\hello-python.exe
```

**Linux / macOS:**
```bash
chmod +x hello-python-linux-x64
./hello-python-linux-x64
```

## ⚙️ CI Pipeline (GitHub Actions)

При каждом push в ветку `main` или тег `v*` автоматически выполняется:

| Шаг | Инструмент | Назначение |
|-----|-----------|------------|
| Lint | `ruff check` | Статический анализ кода |
| Format check | `ruff format --check` | Проверка форматирования |
| Tests | `pytest -v` | Юнит-тесты |
| Matrix Build | PyInstaller `--onefile` | Сборка под 3 ОС параллельно |
| Release | `softprops/action-gh-release` | Публикация бинарников в Releases |

### Теги версий
- `v0.1.0`, `v0.2.0` — семантическое версионирование
- Версия хранится в **двух файлах**: `pyproject.toml` и `hello/__init__.py`
- ⚠️ Оба файла должны совпадать, иначе в бинарнике будет старая версия!

## 📦 Публикация в GitHub Releases

Бинарники автоматически публикуются в **GitHub Releases** при push тега `v*`.



**Доступные платформы:**
- `hello-python-linux-x64` (~19 MB)
- `hello-python-macos-arm64` (~8 MB, Apple Silicon)
- `hello-python-windows-x64.exe` (~8 MB)

> 💡 Релиз создается **только** при push тега (например, `git tag v0.1.0 && git push origin v0.1.0`). Push в ветку `main` запускает только тесты.

## 🔧 Технологии

- **Python 3.12** — язык программирования
- **PyInstaller** — упаковка в один исполняемый файл (`--onefile`)
- **pytest** — фреймворк для тестирования
- **ruff** — быстрый линтер и форматтер
- **GitHub Actions** — CI/CD pipeline с матричными сборками
- **GitHub Releases** — публикация бинарников
- **Docker** — локальная сборка без установки Python
- **Semantic Versioning** — управление версиями через Git-теги

##  Ключевые особенности Python + PyInstaller

В отличие от Go/Rust:
- **Нет cross-compilation** — PyInstaller встраивает платформо-зависимый интерпретатор
- **Нужен отдельный runner для каждой ОС** — Linux, macOS, Windows собираются параллельно в матрице
- **Размер бинарника** — ~8-19 MB (включает Python Runtime)
- **Версия в двух местах** — `pyproject.toml` и `__init__.py` должны совпадать

![загрузка на репозиторий](/images/555.jpg)

![проверка action](/images/666.jpg)
![создание тега](/images/333.jpg)
![проверка тега](/images/111.jpg)
![проверка Releases](/images/333.jpg)
![проверка бинарника](/images/444.jpg)
---

```
