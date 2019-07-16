# lowhaio-sendfile-threads

Uploading files in lowhaio using sendfile and threads

> Work in progress. This README serves as a rough design spec


## Usage

The pool must be created with `lowhaio.Pool` with a `send_body` of `lowhaio_sendfile_threads.SendfileThreadPool`.

```python
from lowhaio import Pool
from lowhaio_sendfile_threads import SendfileThreadPool

send_body, _ = SendfileThreadPool()
request, _ = Pool(send_body=send_body)
```

and the `body` argument of `request` must be a function that returns a context manager that fields one (or more) files.

```python
import contextlib
import os
path = '/path/to/file'

@contextlib.contextmanager
def body(path):
    with open(path) as file:
        yield file

await request(b'GET', 'https://example.com/path', body=body, body_args=(path,))
```

If cleanup is necessary to delete the thread pool, the second returned argument can be used.

```python
send_body, close_thread_pool = SendfileThreadPool()

...

await close_thread_pool()
```
