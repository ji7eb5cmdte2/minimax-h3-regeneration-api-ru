# MiniMax H3 API (`MiniMax-H3-Regeneration`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0343 per second** on the APIMart route — одна OpenAI-совместимая конечная точка на `https://api.apimart.ai/v1`, ежемесячный тариф не нужен. *(данные на 2026-10-12)*

**[Получить API-ключ](https://go.apimart.ai/k-65e7d9)** · **[Актуальные цены](https://go.apimart.ai/k-5fc3c9)** · **[Страница модели](https://go.apimart.ai/k-08d511)** · [⚡ Быстрый старт за 60 секунд](#quickstart)

**Почему команды используют MiniMax H3 (`MiniMax-H3-Regeneration`) через APIMart**

- **Один ключ — весь каталог.** Тот же base URL `https://api.apimart.ai/v1` и тот же заголовок `Authorization` дают доступ к MiniMax H3 (`MiniMax-H3-Regeneration`) и ещё 300+ моделям для изображений, видео и языка — менять нужно поле `model`, а не клиент.
- **Минимум $1, оплата по факту.** Ни подписки, ни предоплаченного тарифа, который нужно заранее рассчитать: пополняйте от $1 и расходуйте на вызовы.
- **Сумма списания возвращается в ответе.** Каждая завершённая задача сообщает списанную сумму (`cost` / `credits_cost`); задача со статусом `failed` возвращается на баланс.
- **Асинхронность по умолчанию.** Отправьте задачу, получите `task_id`, опрашивайте `GET /v1/tasks/{id}` — пакетная обработка и повторы это обычная работа с очередью, а не отдельная интеграция.

<!-- /conv-kit:v1 -->

**Также известен как:** Хайлуо · минимакс видео

MiniMax H3 on APIMart is the latest generation AI video model, offering native 2K output, high motion coherence, multimodal references, and professional-grade performance.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `MiniMax-H3-Regeneration` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-08d511) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.0429 | 0.03432 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-5fc3c9) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-08d511) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"MiniMax-H3-Regeneration","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "MiniMax-H3-Regeneration", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Какой id у модели MiniMax H3?**

`MiniMax-H3-Regeneration`. Смотрите таблицу маршрутов выше.

**Как тарифицируется?**

**from $0.0343 per second** on the APIMart route. Завершённая задача указывает точную сумму в поле `cost`; неудачная задача возвращается на баланс.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-65e7d9) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [minimax-h3-regeneration-prompts-ru](https://github.com/ji7eb5cmdte2/minimax-h3-regeneration-prompts-ru)
- **Same model, other languages** — 10 more language builds: English `minimax-h3-regeneration-api` · 中文 `minimax-h3-regeneration-api-cn` · Deutsch `minimax-h3-regeneration-api-de` · Español `minimax-h3-regeneration-api-es` · Français [minimax-h3-regeneration-api-fr](https://github.com/cpbj5-plstbgc3/minimax-h3-regeneration-api-fr) · Relay/gateway `minimax-h3-regeneration-api-gateway` · Bahasa [minimax-h3-regeneration-api-id](https://github.com/n0btbcff1fuugv/minimax-h3-regeneration-api-id) · 日本語 [minimax-h3-regeneration-api-ja](https://github.com/nxyw1/minimax-h3-regeneration-api-ja) · 한국어 [minimax-h3-regeneration-api-ko](https://github.com/p23t7izq6/minimax-h3-regeneration-api-ko) · Português [minimax-h3-regeneration-api-pt](https://github.com/v38tbia2nk5qae/minimax-h3-regeneration-api-pt)
- **More models in this language** — [midjourney-api-ru](https://github.com/sfpxvicw/midjourney-api-ru) · [gemini-2-5-flash-image-preview-api-ru](https://github.com/zwtdva8/gemini-2-5-flash-image-preview-api-ru) · [nano-banana-api-ru](https://github.com/ggpbi40ss/nano-banana-api-ru) · [gemini-3-1-flash-image-preview-api-ru](https://github.com/lu60av/gemini-3-1-flash-image-preview-api-ru) · [nano-banana-2-api-ru](https://github.com/jnp3hlry/nano-banana-2-api-ru) · [gemini-3-pro-image-preview-api-ru](https://github.com/dbxz24/gemini-3-pro-image-preview-api-ru)
- **Topic hubs** — every build of this model, all languages: [minimax-h3-regeneration](https://github.com/topics/minimax-h3-regeneration) · [minimax-h3-regeneration-api](https://github.com/topics/minimax-h3-regeneration-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-08d511> | `apimart.ai/ru/model/minimax-h3` |
| Live pricing | <https://go.apimart.ai/k-5fc3c9> | `apimart.ai/ru/pricing` |
| Get an API key | <https://go.apimart.ai/k-65e7d9> | `apimart.ai/ru/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

MiniMax H3 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
