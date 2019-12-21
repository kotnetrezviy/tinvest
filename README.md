# T-Invest

```
pip install tinvest
```

```python
import asyncio

import tinvest

TOKEN = "<TOKEN>"

events = tinvest.StreamingEvents()


@events.candle()
async def handle_candle(
    api: tinvest.StreamingApi, payload: tinvest.CandleStreamingSchema
):
    print(payload)


@events.orderbook()
async def handle_orderbook(
    api: tinvest.StreamingApi, payload: tinvest.OrderbookStreamingSchema
):
    print(payload)


@events.instrument_info()
async def handle_instrument_info(
    api: tinvest.StreamingApi, payload: tinvest.InstrumentInfoStreamingSchema
):
    print(payload)


@events.error()
async def handle_error(
    api: tinvest.StreamingApi, payload: tinvest.ErrorStreamingSchema
):
    print(payload)


@events.startup()
async def startup(api: tinvest.StreamingApi):
    await api.candle.subscribe("BBG0013HGFT4", "1min")
    await api.orderbook.subscribe("BBG0013HGFT4", 5, "123ASD1123")
    await api.instrument_info.subscribe("BBG0013HGFT4")


@events.cleanup()
async def cleanup(api: tinvest.StreamingApi):
    await api.candle.unsubscribe("BBG0013HGFT4", "1min")
    await api.orderbook.unsubscribe("BBG0013HGFT4", 5)
    await api.instrument_info.unsubscribe("BBG0013HGFT4")


async def main():
    await tinvest.Streaming(TOKEN, state={"postgres": ...}).add_handlers(events).run()


if __name__ == "__main__":
    try:
        asyncio.run(main())
    except KeyboardInterrupt:
        pass

```

```python
import tinvest

TOKEN = "<TOKEN>"

client = tinvest.SyncClient(TOKEN)
api = tinvest.PortfolioApi(client)

response = api.portfolio_get()
data = response.json()
print(tinvest.PortfolioResponse(**data))
```

```python
import asyncio
import tinvest

TOKEN = "<TOKEN>"

client = tinvest.AsyncClient(TOKEN)
api = tinvest.PortfolioApi(client)


async def request():
    async with api.portfolio_get() as response:
        data = await response.json()
        print(tinvest.PortfolioResponse(**data))


loop = asyncio.get_event_loop()
loop.run_until_complete(request())
```