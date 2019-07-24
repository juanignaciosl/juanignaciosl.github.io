---
layout: post
title: 'Python asyncio'
date: 2019-07-21 07:18:57
categories: ['development']
tags: [Software Development, Python]
excerpt: |
  After a couple of days struggling with Python async, multithreading and so on, I want to write down some notes and references at least for my future self.
image:
  feature: abstracto.jpg
---

I'm working in a project with Python and [AIOHTTP](https://aiohttp.readthedocs.io/en/stable/), so inevitably I was going to hit some threading issues that forced me to learn more about **asyncio**. I've collected in this blogpost some useful links. Please contact me for any other good stuff that you can suggest :)

- Official docs:
  - [Coroutines and tasks](https://docs.python.org/3/library/asyncio-task.html#example-parallel-execution-of-tasks).
  - [Executing code in thread of process pools](https://docs.python.org/3/library/asyncio-eventloop.html#executing-code-in-thread-or-process-pools): contains three snippets for the three main alternatives: running in the default loop's executor, in a custom *thread* pool and a custom *process* pool.
  - [PEP 525](https://www.python.org/dev/peps/pep-0525/#asynchronous-generators).
- [Async Python: The Different Forms of Concurrency](http://masnun.rocks/2016/10/06/async-python-the-different-forms-of-concurrency/): very good summary about the current state of Python parallelism, including GIL explanation and a "Making the Right Choice" recommendation.
- [How to deal with blocking code within asyncio event loop](https://www.slideshare.net/MykolaNovik/how-to-deal-with-blocking-code-within-asyncio-event-loop): recommendations, including lower-level suggestions than usual. 
- [Async IO in Python: A Complete Walkthrough](https://realpython.com/async-io-python/).
- [Explanation](https://stackoverflow.com/questions/14991710/is-concurrent-futures-a-medicine-of-the-gil) about `run_in_executor` using `ThreadPoolExecutor`, which is affected by the [GIL](https://wiki.python.org/moin/GlobalInterpreterLock).
- [Yet another async/await tutorial](https://stackabuse.com/python-async-await-tutorial/).
- [uvloop](https://uvloop.readthedocs.io/), an alternative implementation of the built-in asyncio event loop.
- [asyncio snippets](http://blog.mathieu-leplatre.info/some-python-3-asyncio-snippets.html).
- To explore: Python 3.8 multiprocessing shared memory: [Python 3.8, currently in beta, will have a `multiprocessing.shared_memory` module](https://docs.python.org/3.8/whatsnew/3.8.html#multiprocessing). That might make feasible sharing the connection among processes. I haven't done any research about this, so this can be plain wrong. In addition, the overhead of creating a new process might make this approach prohibitive.

# Extra balls

## Fast.ai

Fast.ai is a library for neural networks that has a useful helper for triggering processes ([via @antor](https://twitter.com/antor/status/1152876193525501952), thanks!):

```python
from fastai.core import parallel

def process_one(item,i):
    return (item**2, i)

result = parallel(process_one, range(100000))
```

## Talks

- [Dynamic languages and Parallelism: How to Go from Broken or Slow to Safe and Efficient](https://app.getpocket.com/read/2668197563): not about Python but dynamic languages in general.

## Related references

- [Notes on structured concurrency, or: Go statement considered harmful](https://vorpus.org/blog/notes-on-structured-concurrency-or-go-statement-considered-harmful/).
