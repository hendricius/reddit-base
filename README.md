Reddit Base
===========

A minimal reddit API client for Ruby.

**Disclaimer:** Assume I'm an untrustworthy fool until 1.0.0. I'll try to
avoid it, but point releases may drop functionality up until that point.

[![Gem Version](https://badge.fury.io/rb/reddit-base.png)](http://badge.fury.io/rb/reddit-base)

Motivation
----------

Managing major versions of an API client can be tricky, especially when the
API us unversioned, inconsistent and outside of your control.

This client library aims to provide minimal support for the reddit API,
reducing the need for frequent breaking changes and to act as the backbone
for more other higher-level API clients.

Installation
------------

Via Rubygems:

```
gem install reddit-base
```

Or in your Gemfile with Bundler:

```
gem reddit-base
```

What it Does
------------

  * Authentication (user/password, cookie, OAuth2 access token).
  * Rate limiting.
  * Modhash handling (reddit's CSRF protection).
  * JSON coersion.
  * Forwarding.
  * Multipart POST.
  * Reddit error wrapping.
  * Retries for common errors (e.g. Gateway Timeout).
  * Request caching.

What it Doesn't
---------------

  * OAuth2 token negotiation.
  * Parsing of Reddit "Things" and "Kinds."
  * Parsing of common attributes like dates and times.
  * HTML entity decoding (beware of "body" and "selftext").

Usage
-----

Retrieve the JSON for a particular endpoint:

```ruby
require 'reddit/base'

client = Reddit::Base::Client.new(user: USERNAME, password: PASSWORD)
client.get('/r/AskReddit')
```

Making a new self post:

```ruby
require 'reddit/base'

client = Reddit::Base::Client.new(user: USERNAME, password: PASSWORD)
client.post('/api/submit', kind: 'self', sr: SUBREDDIT, title: 'Hello,', text: 'World!')
```

### Authentication

Examples:

```ruby
# Username and password.
client = Reddit::Base::Client.new(user: USERNAME, password: PASSWORD)

# Cookie.
client = Reddit::Base::Client.new(cookie: COOKIE)

# OAuth2 access token.
client = Reddit::Base::Client.new(access_token: ACCESS_TOKEN)
```

### File Uploads

For example, uploading an image to a subreddit you moderate:

```ruby
image_upload = Reddit::Base::UploadIO.new('/path/to/your/image.png', 'image/png')
client.post('/api/upload_sr_img.json', r: SUBREDDIT, file: image_upload, header: 0, name: 'example'
```

### Traversal

`Client` returns a type of `Hashie::Mash` so instead of:

```ruby
client.get('/r/AskReddit').body['data']['children']
```

You can do:

```ruby
client.get('/r/AskReddit').body.data.children
```

### Caching

Reddit recommends not request the same endpoint more than once every 30 seconds, and `Client` respects that.

But this can be by passed by passing in a `nocache` parameter, useful if you're polling a frequently-updated endpoint, e.g.:

```ruby
client.get('/r/all/comments', nocache: true)
```

Recommended Reading
-------------------

  * Project Information
    * Changes: [CHANGES.md](https://github.com/dobs/reddit-base/blob/master/CHANGES.md)
  * reddit API
    * API documentation: http://www.reddit.com/dev/api
    * API wiki: https://github.com/reddit/reddit/wiki/API
    * JSON wiki: https://github.com/reddit/reddit/wiki/JSON
    * Project on Github: https://github.com/reddit/reddit

Contributors
------------

  * Maintainer: [Daniel O'Brien](http://github.com/dobs)

This project is copyright 2014 by its contributors, refer to LICENSE file for
licensing information.
