# Flask-GzipBomb

**Flask-GzipBomb** provides an extension of `flask.Response` class: `GzipBombResponse`. It allows to easily create a HTTP response containing gzipped data filled with null characters (`'\0'`) and compressed with varying number of rounds.

This package was inspired by [Christian Haschek](https://blog.haschek.at/) and his article [How to defend your website with ZIP bombs](https://blog.haschek.at/2017/how-to-defend-your-website-with-zip-bombs.html). The intended purpose is to provide a simple tool that can be used to prevent vuln scanners, dictionary attacks and other unwelcommed traffic. As of know, what exactly does it mean is left for the user to decide and implement.

**Note: Any malicious usage of this library is strictly prohibited.**

## Usage
`GzipBombResponse` class accepts the same arguments as `flask.Response` with the addition of `size` parameter. It defines, what should be the size of decompressed content retuned in the HTTP response. `GzipBombResponse` accepts only predefined `size` values:

```python
('1k', '10k', '100k', '1M', '10M', '100M', '1G', '10G')
```
with `k`, `M` and `G` denoting kilobyte, megabyte and gigabyte.

By default `size` is set to `10M`. Passing any other value than those given above will raise a `KeyError`. For protection usage it is recomended to use `size='10G'`.

#### Example
Server:
```python
from flask import Flask
from flask_gzipbomb import GzipBombResponse

app = Flask(__name__)

@app.route('/tiny-bomb')
def gzipped():
    return GzipBombResponse(size='1M')

if __name__ == "__main__":
    app.run()
```

Client:
```python
# Python 3
import gzip
import requests

r = requests.get('http://localhost:5000/tiny-bomb')
print(r.headers['content-encoding']) # 'gzip,gzip'
print(len(r.content))                # 64

data = gzip.decompress(r.content)
data = gzip.decompress(data)
print(len(data))                     # 1048576
```

## License

[MIT](LICENSE)