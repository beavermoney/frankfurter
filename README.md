# Frankfurter

[![CI/CD Pipeline](https://github.com/beavermoney/frankfurter/actions/workflows/ci.yml/badge.svg)](https://github.com/beavermoney/frankfurter/actions/workflows/ci.yml)

[Frankfurter](https://frankfurter.dev) is an open-source currency data API that tracks daily exchange rates from central banks and official sources.

## History coverage

`GET /v2/coverage` describes the default EUR-based daily blended feed. Pass the same `base`, `quotes`, and `providers`
filters as a rates query to get its coverage, for example `/v2/coverage?base=USD&quotes=ZAR&providers=BIS`.
Non-daily provider history remains available through explicit provider queries and does not enter the daily blend.

The existing `/v2/currencies?scope=all` catalogue covers eligible daily sources plus pegged currencies, while
`/v2/currencies?providers=BIS` describes that provider's recognized currencies. Those currency dates, and the dates
in `/v2/providers`, describe publication coverage. They do not establish that a requested base and quote can be
converted at the same time. Taking their minimum can advertise history that the default rates query cannot return.

For a website headline describing the default API, use **`start_date` from `/v2/coverage`**:

```javascript
const coverage = await fetch("https://api.frankfurter.dev/v2/coverage").then(r => r.json());
const headlineYear = coverage.start_date?.slice(0, 4); // Hide the history claim when null.
```

Use matching filters for pair or provider pages. Both bounds are eligible observation dates at which the selected
rates query returns at least one record; they can contain gaps. A carried quote's returned date may precede the
first date its base bridge exists. The bounds do not extend history into future days merely because carry-forward
can answer them. Multiple quotes use the rates endpoint's semantics: any matching pair suffices. No queryable pair
returns null bounds. Coverage accepts only `base`, `quotes`, and `providers`.
