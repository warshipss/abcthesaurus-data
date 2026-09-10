# ABC Thesaurus — open English synonym graph

Sense-level English thesaurus data: **23,184 headwords, 32,878 senses, 340,992 verified relations** — every synonym/antonym attached to a specific meaning and labeled with **register** and **relative intensity**.

Maintained by [abcthesaurus.com](https://abcthesaurus.com/); this repo hosts documentation and loading examples. Data downloads live at **https://abcthesaurus.com/data/**.

## What's in the data

| Field | Values |
|---|---|
| `pos` | noun, verb, adj, adv, prep, pron, conj, other |
| `register` | neutral, formal, informal, slang, dated, literary, technical |
| `intensity` | integer −2…+2, relative to the headword (good → superb = +1) |
| relations | `synonyms`, `near_synonyms`, `antonyms` — all sense-level |

Example record (JSONL, one object per headword):

```json
{"word": "sterling",
 "senses": [{"pos": "adj", "gloss": "of the finest quality",
   "synonyms": [{"word": "excellent",   "register": "neutral",  "intensity": 0},
                {"word": "first rate",  "register": "informal", "intensity": 0},
                {"word": "meritorious", "register": "formal",   "intensity": 0}],
   "near_synonyms": [{"word": "exquisite", "register": "formal", "intensity": 1}],
   "antonyms": [{"word": "second-rate", "register": "neutral", "intensity": 0}]}],
 "examples": ["Tom is doing sterling work."]}
```

## Files

| File | Contents |
|---|---|
| [`abcthesaurus-en.jsonl.gz`](https://abcthesaurus.com/data/abcthesaurus-en.jsonl.gz) | Full graph, one JSON object per headword (2.9 MB) |
| [`abcthesaurus-en-edges.csv.gz`](https://abcthesaurus.com/data/abcthesaurus-en-edges.csv.gz) | Flat table of all 340,992 relations (2.8 MB) |
| [`abcthesaurus-vs-notes.csv.gz`](https://abcthesaurus.com/data/abcthesaurus-vs-notes.csv.gz) | ~4.5k editorial "what's the difference" notes for confusable pairs |

## JSON API

Every word is also served as JSON, no key required:

```
GET https://abcthesaurus.com/api/<word-slug>.json
```

Try it: [`/api/sterling.json`](https://abcthesaurus.com/api/sterling.json). For bulk work please use the dump, not the API.

## Quick start

```python
import gzip, json, urllib.request

url = "https://abcthesaurus.com/data/abcthesaurus-en.jsonl.gz"
urllib.request.urlretrieve(url, "thesaurus.jsonl.gz")

with gzip.open("thesaurus.jsonl.gz", "rt", encoding="utf-8") as f:
    thesaurus = {o["word"]: o for o in map(json.loads, f)}

# all informal synonyms of "drink" (verb senses)
for sense in thesaurus["drink"]["senses"]:
    if sense["pos"] == "verb":
        print(sense["gloss"], "->",
              [s["word"] for s in sense["synonyms"] if s["register"] == "informal"])
```

Pandas, straight from the flat CSV:

```python
import pandas as pd
df = pd.read_csv("https://abcthesaurus.com/data/abcthesaurus-en-edges.csv.gz")
df[(df.relation == "synonym") & (df.register == "slang")].head()
```

## Use cases

- **Formality rewriting / style transfer** — pick a synonym by `register`
- **Paraphrase strength control** — walk edges by `intensity`
- **WSD evaluation** — antonyms are disambiguated per sense
- **Writing tools** — suggestions that won't propose *quaff* in a business email

## License

Data: [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) — attribution with a link to [abcthesaurus.com](https://abcthesaurus.com/) required.
Usage examples inside the data are from [Tatoeba](https://tatoeba.org) (CC BY 2.0 FR).
Code in this repo: MIT.

A line like *"Thesaurus data © [ABC Thesaurus](https://abcthesaurus.com/), CC BY-SA 4.0"* is perfect.
