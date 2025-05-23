# Facebook Scraper

[![PyPI download month](https://img.shields.io/pypi/dm/facebook-scraper.svg)](https://pypi.python.org/pypi/facebook-scraper/)
[![PyPI download week](https://img.shields.io/pypi/dw/facebook-scraper.svg)](https://pypi.python.org/pypi/facebook-scraper/)
[![PyPI download day](https://img.shields.io/pypi/dd/facebook-scraper.svg)](https://pypi.python.org/pypi/facebook-scraper/)

[![PyPI version](https://img.shields.io/pypi/v/facebook-scraper?color=blue)](https://pypi.python.org/pypi/facebook-scraper/)
[![PyPI pyversions](https://img.shields.io/pypi/pyversions/facebook-scraper.svg)](https://pypi.python.org/pypi/facebook-scraper/)
[![GitHub commits since tagged version](https://img.shields.io/github/commits-since/kevinzg/facebook-scraper/v0.2.59)](https://github.com/kevinzg/facebook-scraper/commits/)

[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

Scrape Facebook public pages without an API key. Inspired by [twitter-scraper](https://github.com/kennethreitz/twitter-scraper).

## Install

To install the latest release from PyPI:

```sh
pip install facebook-scraper
```

Or, to install the latest master branch:

```sh
pip install git+https://github.com/pandebono524/facebook-scraper.git
```

## Usage

Send the unique **page name, profile name, or ID** as the first parameter and you're good to go:

```python
>>> from facebook_scraper import get_posts

>>> for post in get_posts('nintendo', pages=1):
...     print(post['text'][:50])
...
The final step on the road to the Super Smash Bros
We’re headed to PAX East 3/28-3/31 with new games
```

### Optional parameters

*(For the `get_posts` function)*.

- **group**: group id, to scrape groups instead of pages. Default is `None`.
- **pages**: how many pages of posts to request, the first 2 pages may have no results, so try with a number greater than 2. Default is 10.
- **timeout**: how many seconds to wait before timing out. Default is 30.
- **credentials**: tuple of user and password to login before requesting the posts. Default is `None`.
- **extra_info**: bool, if true the function will try to do an extra request to get the post reactions. Default is False.
- **youtube_dl**: bool, use Youtube-DL for (high-quality) video extraction. You need to have youtube-dl installed on your environment. Default is False.
- **post_urls**: list, URLs or post IDs to extract posts from. Alternative to fetching based on username.
- **cookies**: One of:
  - The path to a file containing cookies in Netscape or JSON format. You can extract cookies from your browser after logging into Facebook with an extension like [Get cookies.txt LOCALLY](https://chrome.google.com/webstore/detail/get-cookiestxt-locally/cclelndahbckbenkjhflpdbgdldlbecc) or [Cookie Quick Manager (Firefox)](https://addons.mozilla.org/en-US/firefox/addon/cookie-quick-manager/). Make sure that you include both the c_user cookie and the xs cookie, you will get an InvalidCookies exception if you don't.
  - A [CookieJar](https://docs.python.org/3.9/library/http.cookiejar.html#http.cookiejar.CookieJar)
  - A dictionary that can be converted to a CookieJar with [cookiejar_from_dict](https://2.python-requests.org/en/master/api/#requests.cookies.cookiejar_from_dict)
  - The string `"from_browser"` to try extract Facebook cookies from your browser
- **options**: Dictionary of options. Set `options={"comments": True}` to extract comments, set `options={"reactors": True}` to extract the people reacting to the post.
Both `comments` and `reactors` can also be set to a number to set a limit for the amount of comments/reactors to retrieve.
Set `options={"progress": True}` to get a `tqdm` progress bar while extracting comments and replies.
Set `options={"allow_extra_requests": False}` to disable making extra requests when extracting post data (required for some things like full text and image links).
Set `options={"posts_per_page": 200}` to request 200 posts per page. The default is 4.

## CLI usage

```sh
$ facebook-scraper --filename nintendo_page_posts.csv --pages 10 nintendo
```

Run `facebook-scraper --help` for more details on CLI usage.

**Note:** If you get a `UnicodeEncodeError` try adding `--encoding utf-8`.

### Practical example: donwload comments of a post

```python
"""
Download comments for a public Facebook post.
"""

import facebook_scraper as fs

# get POST_ID from the URL of the post which can have the following structure:
# https://www.facebook.com/USER/posts/POST_ID
# https://www.facebook.com/groups/GROUP_ID/posts/POST_ID
POST_ID = "pfbid02NsuAiBU9o1ouwBrw1vYAQ7khcVXvz8F8zMvkVat9UJ6uiwdgojgddQRLpXcVBqYbl"

# number of comments to download -- set this to True to download all comments
MAX_COMMENTS = 100

# get the post (this gives a generator)
gen = fs.get_posts(
    post_urls=[POST_ID],
    options={"comments": MAX_COMMENTS, "progress": True}
)

# take 1st element of the generator which is the post we requested
post = next(gen)

# extract the comments part
comments = post['comments_full']

# process comments as you want...
for comment in comments:

    # e.g. ...print them
    print(comment)

    # e.g. ...get the replies for them
    for reply in comment['replies']:
        print(' ', reply)
```

## Post example

```python
{'available': True,
 'comments': 459,
 'comments_full': None,
 'factcheck': None,
 'fetched_time': datetime.datetime(2021, 4, 20, 13, 39, 53, 651417),
 'image': 'https://scontent.fhlz2-1.fna.fbcdn.net/v/t1.6435-9/fr/cp0/e15/q65/58745049_2257182057699568_1761478225390731264_n.jpg?_nc_cat=111&ccb=1-3&_nc_sid=8024bb&_nc_ohc=ygH2fPmfQpAAX92ABYY&_nc_ht=scontent.fhlz2-1.fna&tp=14&oh=7a8a7b4904deb55ec696ae255fff97dd&oe=60A36717',
 'images': ['https://scontent.fhlz2-1.fna.fbcdn.net/v/t1.6435-9/fr/cp0/e15/q65/58745049_2257182057699568_1761478225390731264_n.jpg?_nc_cat=111&ccb=1-3&_nc_sid=8024bb&_nc_ohc=ygH2fPmfQpAAX92ABYY&_nc_ht=scontent.fhlz2-1.fna&tp=14&oh=7a8a7b4904deb55ec696ae255fff97dd&oe=60A36717'],
 'is_live': False,
 'likes': 3509,
 'link': 'https://www.nintendo.com/amiibo/line-up/',
 'post_id': '2257188721032235',
 'post_text': 'Don’t let this diminutive version of the Hero of Time fool you, '
              'Young Link is just as heroic as his fully grown version! Young '
              'Link joins the Super Smash Bros. series of amiibo figures!\n'
              '\n'
              'https://www.nintendo.com/amiibo/line-up/',
 'post_url': 'https://facebook.com/story.php?story_fbid=2257188721032235&id=119240841493711',
 'reactions': {'haha': 22, 'like': 2657, 'love': 706, 'sorry': 1, 'wow': 123}, # if `extra_info` was set
 'reactors': None,
 'shared_post_id': None,
 'shared_post_url': None,
 'shared_text': '',
 'shared_time': None,
 'shared_user_id': None,
 'shared_username': None,
 'shares': 441,
 'text': 'Don’t let this diminutive version of the Hero of Time fool you, '
         'Young Link is just as heroic as his fully grown version! Young Link '
         'joins the Super Smash Bros. series of amiibo figures!\n'
         '\n'
         'https://www.nintendo.com/amiibo/line-up/',
 'time': datetime.datetime(2019, 4, 30, 5, 0, 1),
 'user_id': '119240841493711',
 'username': 'Nintendo',
 'video': None,
 'video_id': None,
 'video_thumbnail': None,
 'w3_fb_url': 'https://www.facebook.com/Nintendo/posts/2257188721032235'}
```

### Notes

- There is no guarantee that every field will be extracted (they might be `None`).
- Group posts may be missing some fields like `time` and `post_url`.
- Group scraping may return only one page and not work on private groups.
- If you scrape too much, Facebook might temporarily ban your IP.
- The vast majority of unique IDs on facebook (post IDs, video IDs, photo IDs, comment IDs, profile IDs, etc) can be appended to "https://www.facebook.com/" to result in a redirect to the corresponding object.
- Some functions (such as extracting reactions) require you to be logged into Facebook (pass cookies). If something isn't working as expected, try pass cookies and see if that fixes it.
- Reaction Categories (EN): [`like`, `love`, `haha`, `sorry`, `wow`, `angry`, `care`]

## Comment & Reply example
```python
{'comment_id': '1417925635669547', 
 'comment_url': 'https://facebook.com/1417925635669547', 
 'commenter_id': '100009665948953', 
 'commenter_url': 'https://facebook.com/tw0311?eav=AfZuEAOAat6KRX5WFplL0SNA4ZW78Z2O7W_sjwMApq67hZxXDwXh2WF2ezhICX1LCT4&fref=nf&rc=p&refid=52&__tn__=R&paipv=0', 
 'commenter_name': 'someone', 
 'commenter_meta': None, 
 'comment_text': 'something', 
 'comment_time': datetime.datetime(2023, 6, 23, 0, 0), 
 'comment_image': 'https://scontent.ftpe8-2.fna.fbcdn.net/m1/v/t6/An_UvxJXg9tdnLU3Y5qjPi0200MLilhzPXUgxzGjQzUMaNcmjdZA6anyrngvkdub33NZzZhd51fpCAEzNHFhko5aKRFP5fS1w_lKwYrzcNLupv27.png?ccb=10-5&oh=00_AfCdlpCwAg-SHhniMQ16uElFHh-OG8kGGmLAzvOY5_WZgw&oe=64BE3279&_nc_sid=7da55a', 
 'comment_reactors': [
   {'name': 'Tom', 'link': 'https://facebook.com/ryan.dwayne?eav=AfaxdKIITTXyZj4H-eanXQgoxzOa8Vag6XkGXXDisGzh_W74RYZSXxlFZBofR4jUIOg&fref=pb&paipv=0', 'type': 'like'}, 
   {'name': 'Macy', 'link': 'https://facebook.com/profile.php?id=100000112053053&eav=AfZ5iWlNN-EjjSwVNQl7E2HiVp25AUZMqfoPvLRZGnbUAQxuLeN8nl6xnnQTJB3uxDM&fref=pb&paipv=0', 'type': 'like'}],
 'comment_reactions': {'like': 2}, 
 'comment_reaction_count': 2, 
 'replies': [
   {'comment_id': '793761608817229', 
    'comment_url': 'https://facebook.com/793761608817229', 
    'commenter_id': '100022377272712', 
    'commenter_url': 'https://facebook.com/brizanne.torres?eav=Afab9uP4ByIMn1xaYK0UDd1SRU8e5Zu7faKEx6qTzLKD2vp_bB1xLDGvTwEd6u8A7jY&fref=nf&rc=p&__tn__=R&paipv=0', 
    'commenter_name': 'David', 
    'commenter_meta': None, 
    'comment_text': 'something', 
    'comment_time': datetime.datetime(2023, 6, 23, 18, 0), 
    'comment_image': None, 
    'comment_reactors': [], 
    'comment_reactions': {'love': 2}, 
    'comment_reaction_count': None}
 ]
}
```


## Profiles

The `get_profile` function can extract information from a profile's about section. Pass in the account name or ID as the first parameter.
Note that Facebook serves different information depending on whether you're logged in (cookies parameter), such as Date of birth and Gender. Usage:

```python
from facebook_scraper import get_profile
get_profile("zuck") # Or get_profile("zuck", cookies="cookies.txt")
```

Outputs:

```python
{'About': "I'm trying to make the world a more open place.",
 'Education': 'Harvard University\n'
              'Computer Science and Psychology\n'
              '30 August 2002 - 30 April 2004\n'
              'Phillips Exeter Academy\n'
              'Classics\n'
              'School year 2002\n'
              'Ardsley High School\n'
              'High School\n'
              'September 1998 - June 2000',
 'Favourite Quotes': '"Fortune favors the bold."\n'
                     '- Virgil, Aeneid X.284\n'
                     '\n'
                     '"All children are artists. The problem is how to remain '
                     'an artist once you grow up."\n'
                     '- Pablo Picasso\n'
                     '\n'
                     '"Make things as simple as possible but no simpler."\n'
                     '- Albert Einstein',
 'Name': 'Mark Zuckerberg',
 'Places lived': [{'link': '/profile.php?id=104022926303756&refid=17',
                   'text': 'Palo Alto, California',
                   'type': 'Current town/city'},
                  {'link': '/profile.php?id=105506396148790&refid=17',
                   'text': 'Dobbs Ferry, New York',
                   'type': 'Home town'}],
 'Work': 'Chan Zuckerberg Initiative\n'
         '1 December 2015 - Present\n'
         'Facebook\n'
         'Founder and CEO\n'
         '4 February 2004 - Present\n'
         'Palo Alto, California\n'
         'Bringing the world closer together.'}
```

To extract friends, pass the argument `friends=True`, or to limit the amount of friends retrieved, set `friends` to the desired number.

## Group info

The `get_group_info` function can extract info about a group. Pass in the group name or ID as the first parameter.
Note that in order to see the list of admins, you need to be logged in (cookies parameter).

Usage:

```python
from facebook_scraper import get_group_info
get_group_info("makeupartistsgroup") # or get_group_info("makeupartistsgroup", cookies="cookies.txt")
```

Output:

```python
{'admins': [{'link': '/africanstylemagazinecom/?refid=18',
             'name': 'African Style Magazine'},
            {'link': '/connectfluencer/?refid=18',
             'name': 'Everythingbrightandbeautiful'},
            {'link': '/Kaakakigroup/?refid=18', 'name': 'Kaakaki Group'},
            {'link': '/opentohelp/?refid=18', 'name': 'Open to Help'}],
 'id': '579169815767106',
 'members': 6814229,
 'name': 'HAIRSTYLES',
 'type': 'Public group'}
```

## Write to a CSV file directly

The library also provides a `write_posts_to_csv()` function that writes posts directly to the disk and is able to resume scraping from the address of the last page. It is very useful when scraping large pages as the data is saved continuously and scraping can be resumed in case of an error. Here is an example to fetch the posts of a group 100 pages at a time and save them in separate files.

```python
import facebook_scraper as fs

# Saves the first 100 pages
for i in range(1, 101):
    fs.write_posts_to_csv(
        group=GROUP_ID, # The method uses get_posts internally so you can use the same arguments and they will be passed along
        page_limit=100,
        timeout=60,
        options={
            'allow_extra_requests': False
        },
        filename=f'./data/messages_{i}.csv', # Will throw an error if the file already exists
        resume_file='next_page.txt', # Will save a link to the next page in this file after fetching it and use it when starting.
        matching='.+', # A regex can be used to filter all the posts matching a certain pattern (here, we accept anything)
        not_matching='^Warning', # And likewise those that don't fit a pattern (here, we filter out all posts starting with "Warning")
        keys=[
            'post_id',
            'text',
            'timestamp',
            'time',
            'user_id'
        ], # List of the keys that should be saved for each post, will save all keys if not set
        format='csv', # Output file format, can be csv or json, defaults to csv
        days_limit=3650 # Number of days for the oldest post to fetch, defaults to 3650
    )

```

## To-Do

- Async support
- ~~Image galleries~~ (`images` entry)
- ~~Profiles or post authors~~ (`get_profile()`)
- ~~Comments~~ (with `options={'comments': True}`)

## Alternatives and related projects

- [facebook-post-scraper](https://github.com/brutalsavage/facebook-post-scraper). Has comments. Uses Selenium.
- [facebook-scraper-selenium](https://github.com/apurvmishra99/facebook-scraper-selenium). "Scrape posts from any group or user into a .csv file without needing to register for any API access".
- [Ultimate Facebook Scraper](https://github.com/harismuneer/Ultimate-Facebook-Scraper).  "Scrapes almost everything about a Facebook user's profile". Uses Selenium.
- [Unofficial APIs](https://github.com/Rolstenhouse/unofficial-apis). List of unofficial APIs for various services, none for Facebook for now, but might be worth to check in the future.
- [major-scrapy-spiders](https://github.com/talhashraf/major-scrapy-spiders). Has a profile spider for Scrapy.
- [facebook-page-post-scraper](https://github.com/minimaxir/facebook-page-post-scraper). Seems abandoned.
- [FBLYZE](https://github.com/isaacmg/fb_scraper). Fork (?).
- [RSSHub](https://github.com/DIYgod/RSSHub/blob/master/lib/routes/facebook/page.js). Generates an RSS feed from Facebook pages.
- [RSS-Bridge](https://github.com/RSS-Bridge/rss-bridge/blob/master/bridges/FacebookBridge.php). Also generates RSS feeds from Facebook pages.
