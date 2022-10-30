# Japan Train Stations

## Setup
### Optional: Conda
One-time setup:
```bash
conda create -n japan-train-stations
conda activate japan-train-stations
conda install -c conda-forge python
python -m pip install wikipedia-api
```

Then, every time you open a new shell terminal, just run
```bash
conda activate japan-train-stations
```

## Run
https://en.wikipedia.org/wiki/List_of_railway_stations_in_Japan
```py
import wikipediaapi
w = wikipediaapi.Wikipedia('en', extract_format=wikipediaapi.ExtractFormat.WIKI)
index = w.page('List of railway stations in Japan')
# index = w.page('Template:Index_Railway_Stations_Japan')
assert index.exists()
index.text
links = {k:v for k, v in index.links.items() if 'Japan:' in k}
assert len(links) > 15 # some may be combined

all_links = []
for link in links.values():
  sublinks = []
  for k,v in link.links.items():
    if 'List of railway stations in Japan' not in k:
      print(k)
      # ja = None
      # this does a bunch of requests and can be slow
      ja = v.langlinks.get('ja', None)
      sleep(.1)
      sublinks.append(dict(en=k,ja=ja.title if ja else None))
  all_links += sublinks

with open('all.txt', 'w') as fid:
  fid.write('\n'.join(f"{x['ja']},{x['en']}" for x in all_links))
```
