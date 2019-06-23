# [TinyURL](<https://leetcode.com/discuss/interview-question/system-design/124658/Design-a-URL-Shortener-(-TinyURL-)-System>)

> Design a service like TinyURL, a URL shortening service, a web service that provides short aliases for redirection of long URLs.

## API

I think it is enough to use RESTful API.

### Generate a new URL

#### Request

```text
PUT /
Content-Type: text/plain

URL
```

#### Response

```text
200 OK
Content-Type: text/plain

http://service-domain/hashcode
```

### Redirect to actual URL from a tiny URL

#### Request

```text
GET /hashcode
```

#### Response

```text
301 Moved Permanently
Location: actual address
```

## Specification

### URL

`http://service-domain/hashcode`

### Hash code

If we use `[0-9A-Za-z]` characters, we need to 7 characters at least to cover 1.5B urls that is [total number of urls in the world](https://www.internetlivestats.com/total-number-of-websites/).

`62^7 = 3521614606208 = 3.5T urls`

Of course, it is vulnerable due to be inferenced easily.

## System (first)

First, 2-tier for this, API Server and Database in simple manner.

### API Server

#### Read

When `GET` reqeust has come,

1. Find the actual URL using `hashcode` by querying to the database.
2. Send a response for redirection.

#### Write

When `PUT` request has come,

1. Calculate a `hashcode` for URL.
2. Try to put it into the database.
3. If failed, goto step `1`.
4. Send a response with a newly created tiny URL.

There is a loop between step `1` and `3`, so a hash function should be enough to be fair and it should provide a different value each time.

#### Ops

TBC

### Database

It has a very simple tuple `(hashcode, url)` where `hashcode` is 7 characters and the mean of `url` is [about 77](https://stackoverflow.com/questions/6168962/typical-url-lengths-for-storage-calculation-purposes-url-shortener).

## Questions

- What's happened while scaling up and down?
- Is this system generated the same tinyURL from the same input URLs?
- Could you give the TTL to tinyURL?
- Could you reduce more length from your tinyURL?
