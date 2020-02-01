
Waddling through the Scrapy codebase


First commit 2008

https://github.com/scrapy/scrapy/commit/0ae2f10e21913f2f9034a219bb6c54877ecf7c03


Next commit website

https://github.com/scrapy/scrapy/commit/468c0d38fa0cf99abf9dbe6a2f388806ce51083c

Scrapy codebase commited
https://github.com/scrapy/scrapy/commit/83dcf8aff91d0178ffbf4978fb757fc7e6d5d08c

files:
https://github.com/scrapy/scrapy/tree/83dcf8aff91d0178ffbf4978fb757fc7e6d5d08c

Navigating large codebases

https://www.quora.com/Whats-a-good-way-to-read-computer-code-that-you-know-almost-nothing-about-say-Linux-How-do-you-understand-something-when-you-constantly-have-to-drill-into-lower-levels-and-branches-and-it-gets-convoluted-Wont-you

What's a good way to read computer code that you know almost nothing about (say Linux)? How do you understand something when you constantly have to drill into lower levels and branches, and it gets convoluted? Won't you need expert help?

"Point two: Find a minimal version to locate the core."

---

You need:
 * Python 2.5
 * Twisted 
Twisted is an open source network framework written entirely in Python. It allows you to create a SMTP, HTTP, proxy and ssh servers (and more) in Python with minimal effort. Twisted is Asynchronous and event driven and allows applications to respond to different network connection without the use of traditional threading models.

 Asynchronous programming is a means of parallel programming in which a unit of work runs separately from the main application thread and notifies the calling thread of its completion, failure or progress.

 * libxml2 python bindings. version 2.6.28 or above is highly recommended.

 libxml is written in C

 * pyopenssl for HTTPS support
   * for win32: http://webcleaner.sourceforge.net/pyOpenSSL-0.6.win32-py2.5.exe
pyOpenSSL is a rather thin wrapper around (a subset of) the OpenSSL library. With thin wrapper we mean that a lot of the object methods do nothing more than calling a corresponding function in the OpenSSL library.

OpenSSL is a robust, commercial-grade, and full-featured toolkit for the Transport Layer Security (TLS) and Secure Sockets Layer (SSL) protocols. It is also a general-purpose cryptography library. 

 * spidermonkey (optional) - required for JSParser
SpiderMonkey is Mozilla's JavaScript engine written in C and C++. It is used in various Mozilla products, including Firefox, and is available under the MPL2.

JSParser: A python 2.7 script using Tornado and JSBeautifier to parse relative URLs from JavaScript files. Useful for easily discovering AJAX requests when performing security research or bug bounty hunting.
More details: https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey/Internals

---



---
Lesson 1: Checking Python Version

in scrapy/trunk/scrapy/__init__.py we see

```
if sys.version_info < (2,5):
    print "Scrapy %s requires Python 2.5 or above" % __version__
    sys.exit(1)
```

An online solution:
```
import subprocess

#b'Python 3.7.3\r\n'
pythonVersion = subprocess.check_output(["python", "--version"])
pythonVersion = pythonVersion.decode() # utf-8
```

use sys.executable


```
>>> sys.version_info
>>> sys.version_info.major
>>> sys.version_info[0]
```

---

Lesson 2:
Question: How do you know which Python version the code is using just by looking at the code?

---

Lesson 3:
Question: What is the use of from __future__ import ?

---

Lesson 4:
What is the purpose of
```
"%s: %s" % (cmdname, cmd.short_desc())
```
?

---
Lesson 5:
scrapy/trunk/scrapy/command/commands/scrape.py

Handling imports

```
try: # use IPython if available
    import IPython
    shell = IPython.Shell.IPShell(argv=[], user_ns=vars)
    shell.mainloop()
except ImportError:
    import code
```

Q: Why and why not import in-code?

---

Lesson 6:

What is the purpose of the following code?

class Command(ScrapyCommand)

scrapy/trunk/scrapy/command/commands/__init__.py

---

Lesson 7:
What is the purpose of th init file?
https://stackoverflow.com/questions/448271/what-is-init-py-for, answer by flycee
https://stackoverflow.com/questions/37139786/is-init-py-not-required-for-packages-in-python-3-3

---

Lesson 8:

scrapy/scrapy/trunk/scrapy/spider/__init__.py

from scrapy.spider.models import BaseSpider
from scrapy.spider.manager import SpiderManager

spiders = SpiderManager()

---

Lesson 9:

scrapy/trunk/scrapy/command/commands/replay.py

        s = "Replay a session previously recorded with crawl --record\n"
        s += "\n"
        s += "Available actions:\n"
        s += "  crawl: just replay the crawl (default if action omitted)\n"
        s += "  diff: replay the crawl and show differences in items scraped/passed\n"
        s += "  update: replay the crawl and update both scraped and passed items\n"
        s += "  showitems: show items stored\n"
        s += "  showpages: show all responses downloaded (not only HTML pages)\n"

neat way to add long strings

Q: How to do so without s +=?

---

Lesson 10:

scrapy/trunk/scrapy/conf/core_settings.py

Why all are in caps?

BOT_NAME = 'scrapy'
BOT_VERSION = scrapy.__version__

ENGINE_DEBUG = False

# Download configuration options
USER_AGENT = '%s/%s' % (BOT_NAME, BOT_VERSION)
DOWNLOAD_TIMEOUT = 180      # 3mins
CONCURRENT_DOMAINS = 8    # number of domains to scrape in parallel
REQUESTS_PER_DOMAIN = 8     # max simultaneous requests per domain

LOG_ENABLED = True  #
LOGLEVEL = 'DEBUG'   # default loglevel
LOGFILE = None      # None means sys.stderr by default
LOG_STDOUT = False   #
...

---

Lesson 11:

Which one is right, 

DOWNLOAD_TIMEOUT = 180      # 3mins

or

DOWNLOAD_TIMEOUT = 180      #  3mins?

---

Lesson 12:

scrapy/trunk/scrapy/contrib/cluster/master/manager.py

What does @property means?

---

Lesson 12:

Why is there a _ in
def _dispatch_domains(self, domains): ?

---

Lesson 13: 
scrapy/trunk/scrapy/contrib/downloadermiddleware/cache.py

use of x<y<z

if not 200 <= int(response.status) < 300:
                raise HttpException(response.status, None, response)

---

scrapy/trunk/scrapy/contrib/downloadermiddleware/redirect.py

META_REFRESH_RE = re.compile(r'<meta[^>]*http-equiv[^>]*refresh[^>].*?(\d+);url=([^"\']+)', re.IGNORECASE)

---

TWISTED/SCRAPY RESOURCES

http://krondo.com/an-introduction-to-asynchronous-programming-and-twisted/

Asyncio or Twisted: https://us.pycon.org/2016/schedule/presentation/2114/

The details: https://lwn.net/Articles/692254/


https://speakerdeck.com/sibiryakov/scrapy-internals

https://stackoverflow.com/questions/35111265/how-does-pythons-twisted-reactor-work

https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/heaps.html

---

https://docs.scrapy.org/en/latest/topics/architecture.html

https://medium.com/@innovationchef/scrapyman-6f2139447373

https://medium.com/@innovationchef/scrapyman-part-2-dfe806c06c39

https://medium.com/@innovationchef/scrapyman-part-3-22cdb3e0a149




