# T-Invest

[![Build Status](https://api.travis-ci.com/daxartio/tinvest.svg?branch=master)](https://travis-ci.com/daxartio/tinvest)
[![PyPI](https://img.shields.io/pypi/v/tinvest)](https://pypi.org/project/tinvest/)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/tinvest)](https://www.python.org/downloads/)
[![Codecov](https://img.shields.io/codecov/c/github/daxartio/tinvest)](https://travis-ci.com/daxartio/tinvest)
[![GitHub last commit](https://img.shields.io/github/last-commit/daxartio/tinvest)](https://github.com/daxartio/tinvest)
[![Tinvest](https://img.shields.io/github/stars/daxartio/tinvest?style=social)](https://github.com/daxartio/tinvest)

```
pip install tinvest
```

Данный проект представляет собой инструментарий на языке Python для работы с OpenAPI Тинькофф Инвестиции, который можно использовать для создания торговых роботов.

Клиент предоставляет синхронный и асинхронный API для взаимодействия с Тинькофф Инвестиции.

Есть возможность делать запросы через командную строку, подробнее [тут](https://daxartio.github.io/tinvest/cli/).

```
pip install tinvest[cli]

# Пример использования
tinvest openapi --token TOKEN portfolio
```

Performance

```
pip install tinvest[uvloop]
pip install tinvest[orjson]
```

[orjson](https://github.com/ijl/orjson)
[uvloop](https://github.com/MagicStack/uvloop)


## Начало работы

### Где взять токен аутентификации?

В разделе инвестиций вашего [личного кабинета tinkoff](https://www.tinkoff.ru/invest/). Далее:

* Перейдите в настройки
* Проверьте, что функция "Подтверждение сделок кодом" отключена
* Выпустите токен для торговли на бирже и режима "песочницы" (sandbox)
* Скопируйте токен и сохраните, токен отображается только один раз, просмотреть его позже не получится, тем не менее вы можете выпускать неограниченное количество токенов

## Документация

[tinvest](https://daxartio.github.io/tinvest/)

[invest-openapi](https://tinkoffcreditsystems.github.io/invest-openapi/)

### Быстрый старт

Для непосредственного взаимодействия с OpenAPI нужно создать клиента. Клиенты разделены на streaming и rest.

Примеры использования SDK находятся [ниже](#Примеры).

### У меня есть вопрос

[Основной репозиторий с документацией](https://github.com/TinkoffCreditSystems/invest-openapi/) — в нем вы можете задать вопрос в Issues и получать информацию о релизах в Releases.
Если возникают вопросы по данному SDK, нашёлся баг или есть предложения по улучшению, то можно задать его в Issues.

## Примеры

Для работы с данным пакетом вам нужно изучить [OpenAPI Тинькофф Инвестиции](https://tinkoffcreditsystems.github.io/invest-openapi/swagger-ui/)

### Streaming

Предоставляет асинхронный интерфейс.

> При сетевых сбоях будет произведена попытка переподключения.

```python
import asyncio
import tinvest as ti


async def main():
    async with ti.Streaming('TOKEN') as streaming:
        await streaming.candle.subscribe('BBG0013HGFT4', ti.CandleResolution.min1)
        await streaming.orderbook.subscribe('BBG0013HGFT4', 5)
        await streaming.instrument_info.subscribe('BBG0013HGFT4')
        async for event in streaming:
            print(event)


asyncio.run(main())
```

### Синхронный REST API Client

Для выполнения синхронных http запросов используется библиотека `requests`.
С описанием клиентов можно ознакомиться по этой [ссылке](https://daxartio.github.io/tinvest/tinvest/clients/).

```python
import tinvest

TOKEN = "<TOKEN>"

client = tinvest.SyncClient(TOKEN)

response = client.get_portfolio()  # tinvest.PortfolioResponse
print(response.payload)
```

```python
# Handle error
...
client = tinvest.SyncClient(TOKEN)

try:
    response = client.get_operations("", "")
except tinvest.BadRequestError as e:
    print(e.response)  # tinvest.Error
```

### Асинхронный REST API Client

Для выполнения асинхронных http запросов используется библиотека `aiohttp`.
Клиенты имеют такой же интерфейс как в синхронной реализации, за исключением того,
что функции возвращают объект корутина.

```python
import asyncio
import tinvest

TOKEN = "<TOKEN>"


async def main():
    client = tinvest.AsyncClient(TOKEN)
    response = await client.get_portfolio()  # tinvest.PortfolioResponse
    print(response.payload)

    await client.close()

asyncio.run(main())
```

### Sandbox

Sandbox позволяет вам попробовать свои торговые стратегии, при этом не тратя реальные средства. Протокол взаимодействия полностью совпадает с Production окружением.

```python
client = tinvest.AsyncClient(SANDBOX_TOKEN, use_sandbox=True)
# client = tinvest.SyncClient(SANDBOX_TOKEN, use_sandbox=True)
```

## Environments

| name                  | required | default |
|-----------------------|:--------:|--------:|
| TINVEST_TOKEN         | optional |      '' |
| TINVEST_SANDBOX_TOKEN | optional |      '' |
| TINVEST_USE_ORJSON    | optional |    True |
| TINVEST_USE_UVLOOP    | optional |    True |

## Contributing

Предлагайте свои пулл реквесты, проект с открытым исходным кодом.

### Установка зависимостей

```
python install poetry
make install
```

### Запуск тестов

```
make test
```

### Автоформатирование кода

```
make format
```

### Проверка кода

```
make lint
```

### Релизы

Сборка проекта происходит автоматически по установленному `git tag`.

Также необходимо указать версию в файлах

```
tinvest/
  __init__.py
pyproject.toml
```
