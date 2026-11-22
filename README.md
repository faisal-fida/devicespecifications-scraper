# devicespecifications-scraper

A Scrapy spider that collects phone and tablet specifications from devicespecifications.com and stores them in MongoDB.

## Features

- Starts at the site home page, follows each brand listing, then each device page.
- Parses every specification table on a device page into a nested dictionary (section, then row), with numbered keys when a row has several values.
- Discards pages that did not load fully (fewer than 5 fields).
- Fetches pages through the ZenRows scraping API (premium proxy) using your API key.
- Skips already visited device pages with `scrapy-crawl-once`.
- Saves each device as one MongoDB document.

## Tech stack

Python 3.8+, Scrapy, scrapy-crawl-once, pymongo, python-dotenv, MongoDB, ZenRows API.

## Project structure

```
settings.py                          Scrapy and MongoDB settings, loads .env
pipelines.py                         MongoDB pipeline
items.py, middlewares.py             Scrapy defaults (unused)
spiders/DisplaySpecifications_sipder.py   the spider (name: devicespecifications)
requirements.txt
```

## Setup and run

Requirements: Python, a running MongoDB, and a ZenRows API key.

The Scrapy settings refer to the module name `DisplaySpecifications`, so the code must live in a folder with that name, with a `scrapy.cfg` next to it:

```bash
mkdir scraper && cd scraper
git clone <this repo url> DisplaySpecifications
printf '[settings]\ndefault = DisplaySpecifications.settings\n' > scrapy.cfg
pip install -r DisplaySpecifications/requirements.txt
```

Environment variables (put them in a `.env` file in the folder you run from):

| Name | Purpose |
|------|---------|
| `API_KEY` | ZenRows API key used to fetch pages |

MongoDB host, port, database and collection are set in `settings.py` (`MONGODB_SERVER`, `MONGODB_SERVER_PORT`, `MONGODB_DATABASE`, `MONGODB_COLLECTION`; defaults: localhost:27017).

Run:

```bash
scrapy crawl devicespecifications
```

## Limitations and status

- The packaging is rough: the flat layout needs the folder name and `scrapy.cfg` described above. Not tested end to end here, since it needs a paid API key and a MongoDB.
- `ROBOTSTXT_OBEY` is `False` and `DOWNLOAD_TIMEOUT` is very high (30000 seconds). Review these and the site's terms before running.
- Requests cost ZenRows credits (premium proxy).
- `items.py` and `middlewares.py` are the Scrapy templates and are not used.

## License

MIT, see [LICENSE](LICENSE).
