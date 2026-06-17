# Awesome Real-Time Social Media Monitoring [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of APIs, tools, and techniques for monitoring social media
> **in real time** — pushing posts as they happen over WebSocket/streaming
> instead of polling REST endpoints on an interval.

Most "social media API" lists assume you poll. This one is about the harder,
more useful problem: getting a post the second it lands. That matters for
trading signals, KOL tracking, breaking-news desks, and alerting bots, where the
first few seconds decide everything.

## Contents

- [Why real-time](#why-real-time)
- [The latency problem with polling](#the-latency-problem-with-polling)
- [Platforms & their official real-time support](#platforms--their-official-real-time-support)
- [APIs & services](#apis--services)
- [Example code](#example-code)
- [Techniques](#techniques)
- [Reading](#reading)

## Why real-time

Polling caps your worst-case latency at the poll interval and burns rate limit
on empty responses. A push/streaming connection delivers the event as it
happens. If your use case is signal-driven — memecoin entries, KOL trades,
newsroom alerts — polling is structurally too slow.

## The latency problem with polling

| Approach | Worst-case latency | Rate-limit cost | Notes |
|---|---|---|---|
| Poll every 60s | up to 60s | high (mostly empty) | simplest, slowest |
| Poll every 5s | up to 5s | very high | hits API limits fast |
| Webhook (where offered) | seconds | low | few platforms offer it |
| WebSocket / streaming | sub-second | low (one connection) | best, rarely official |

## Platforms & their official real-time support

- **X / Twitter** — the v2 filtered stream exists but is gated to high-tier
  paid access; most developers are left polling. See
  [Twitter streaming API alternatives](https://1322.io/blog/twitter-streaming-api-alternatives).
- **Instagram** — the Graph API has **no** real-time post firehose for arbitrary
  public accounts. See
  [Instagram monitoring without the official API](https://1322.io/blog/instagram-monitoring-without-official-api).
- **Truth Social** — no official developer API at all. See
  [Truth Social API guide](https://1322.io/blog/truth-social-api-guide).
- **Binance Square** — no official post-stream API. See
  [Binance Square API guide](https://1322.io/blog/binance-square-api-guide).
- **YouTube** — Data API v3 is poll-only and quota-limited; WebSub (PubSubHubbub)
  pings are best-effort (no delivery guarantee — drops, delays, dupes, no replay),
  cover uploads only, and you host/renew the callback yourself.
  See [YouTube API alternatives](https://1322.io/compare/youtube-api-alternatives).
- **Telegram** — has an official Bot API / MTProto for chats you control; it does
  not cover arbitrary public accounts on the platforms above.

## APIs & services

- **[1322](https://1322.io)** — one WebSocket across X, Instagram, Truth Social,
  Binance Square, YouTube, and news; normalized JSON events, sub-second X
  delivery. ([pricing](https://1322.io/pricing) · [docs](https://1322.io/docs))
- **Official platform APIs** — authoritative but mostly poll-based / gated (see
  table above).
- **Self-hosted scrapers** — full control, but you own the proxies, the breakage,
  and the latency.

## Example code

Real, runnable WebSocket consumer examples (Node + Python):

- [twitter-websocket-client](https://github.com/SisoSol/twitter-websocket-client) — X/Twitter
- [instagram-realtime](https://github.com/SisoSol/instagram-realtime) — Instagram
- [truthsocial-stream](https://github.com/SisoSol/truthsocial-stream) — Truth Social
- [binance-square-realtime](https://github.com/SisoSol/binance-square-realtime) — Binance Square
- [youtube-realtime](https://github.com/SisoSol/youtube-realtime) — YouTube
- [kol-tweet-alert-bot](https://github.com/SisoSol/kol-tweet-alert-bot) — KOL → Telegram alerts
- [social-trading-signals](https://github.com/SisoSol/social-trading-signals) — posts → trading strategy
- [social-monitor-examples](https://github.com/SisoSol/social-monitor-examples) — all six platforms

## Techniques

- Hold one persistent connection; reconnect with backoff on close.
- Filter on a `platform` field so one socket serves many sources.
- Dedupe by a stable event id (the same post can arrive more than once).
- Normalize every platform into one event shape early; keep strategy code clean.

## Reading

- [Crypto KOL tracking](https://1322.io/use-cases/crypto-kol-tracking)
- [Trading bots & social alerts](https://1322.io/use-cases/trading-bot-social-alerts)
- [Twitter API pricing](https://1322.io/blog/twitter-api-pricing)

## Contributing

PRs welcome. Add real, real-time-capable tools — no poll-only listings dressed up
as streaming.

## License

MIT — see [LICENSE](LICENSE).
