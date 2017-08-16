# api-specs
Specifications for the microdata API (unnamed)

## Index
- [Overview](#overview)
- [Relevant Resources](#relevant-resources)
- [Request structure](#request-structure)
- [Examples](#examples)
- [Advanced Examples](#advanced-example)
- [Submitting Complex Objects](#submitting-complex-objects)
- [Collections and Lists](#collections-and-lists)
- [Contributing](./docs/CONTRIBUTING.md)

## Overview
> Most websites and apps are, at their core, displaying variable content inside
> of templates. Social networking sites have posts (*perhaps with photos or videos*),
> which may have some comments. E-commerce sites have products with photos and
> descriptions, along with reviews. News sites have articles written by authors,
> again with comments. You might also see information about people, organizations,
> places and events. Ultimately, the web is full of information about things, with
> content placed within templates.

> Numerous APIs and templating languages have been created to address this, but
> developers are then required to learn multiple APIs with different structures
> and methods, different templating languages, etc. There is no standard. APIs
> cannot share resources and context. In other words, existing APIs are essentially
> proprietary and of limited usefulness.

> Google, Microsoft, Yahoo and Yandex founded [schema.org](https://schema.org) in
> an attempt to provide search engines better understanding of pages through
> `itemtype` and `itemprop` attributes in HTML, but they also extended JSON into
> something called JSON-LD, as a pure data representation. Combining this
> relation between data and `<tempalte>`s (*possibly loaded via `<link rel="import"/>`*),
> and providing a REST API for managing data would allow developers to create and
> maintain applications simply by creating templates with the appropriate content
> and attributes, and filling them in with data obtainable through a simple `GET`
> request. Creating data can be created through a simple `POST`. Objects/properties
> directly map from `<input>`s, to database fields, to properties of an AJAX request,
> to templates in HTML (*and are well documented*).

> This would, not only make creating apps much easier, but it would also
> allow for creating complex content that is very descriptive and also defines
> the relation between different representations of data.

**This is a non-code repository for describing the structure and functionality
of the API, as well as how mapping data to content will take place.**

## Relevant Resources
- [Schema.org](https://schema.org)
- [`<template>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template)
- [Full schema list](https://schema.org/docs/full.html)
- [HTML Imports](https://www.html5rocks.com/en/tutorials/webcomponents/imports/)

## Request Structure
Uses typical REST methods with endpoints mirroring vocabulary and schema defined
on [schema.org](https://schema.org/docs/full.html)

- `GET /Person/dc67d265e8b544b62f8fea85083fde45` Get an individual entry
- `GET /Person/dc67d265e8b544b62f8fea85083fde45?fields=givenName,familyName`
Restrict response data
- `GET /Person/?familyName=Smith&limit=15&offset=16` Get a list of 15 entries with the last
name, "Smith", starting with the 16th entry (for pagination)
- `POST /Person/` Create a new entry. Responds with hash id
(requires form data or JSON)
- `PUT /Person/dc67d265e8b544b62f8fea85083fde45` Update an entry
(requires form data or JSON)
- `DELETE /Person/dc67d265e8b544b62f8fea85083fde45` Delete an entry
- `HEAD /ImageObject/ca29845ff732289185379d697b8d4b86` Get meta data (might useful for ImageObjects, etc.)

## Examples

- [Full documentation](https://schema.org/Person)
- Response to be given
```json
{
    "@type": "Person",
    "@context": "http://schema.org",
    "givenName": "Chris",
    "familyName": "Zuber"
}
```

- Basic HTML with template
```html
<script src="//cdn.domain.tld/js/schema.js"></script>
<!--
    `data-import` specifies both a data source, as well as a template to use for
    the returned data through use of the URL's hash.

    Note that the URL structure of the REST API mimics that of schema.org's definitions.
-->
<div data-import="//api.domain.tld/Person/dc67d265e8b544b62f8fea85083fde45#person-template"></div>
<template id="person-template">
    <div itemtype="http://schema.org/Person">
        <span itemprop="givenName"></span>
        <span itemprop="familyName"></span>
        <!-- ... -->
    </div>
</person>
```

- HTML using [`<link rel="import">`](https://www.html5rocks.com/en/tutorials/webcomponents/imports/)
```html
<script src="//cdn.domain.tld/js/schema.js"></script>
<!-- Loads template as seen in above example -->
<link rel="import" href="//cdn.domain.tld/templates/person.html" name="person-template" />
<div data-import="//api.domain.tld/Person/dc67d265e8b544b62f8fea85083fde45#person-template"></div>
```

- Creating content
```html
<form action="//api.domain.tld/Person" method="POST">
    <input type="text" name="givenName" placeholder="First Name" required="" />
    <br />
    <input type="text" name="familyName" placeholder="Last Name" required="" />
    <!-- ... -->
</form>
```

## Advanced Example

```json
{
    "@type": "Article",
    "@context": "http://schema.org",
    "headline": "Lorem Ipsum",
    "description": "...",
    "keywords": "a, b, ...",
    "articleBody": "Laboriosam in molestiae consequatur maxime.",
    "wordCount": 1378,
    "dateCreated": "2017-08-12T14:32:01",
    "thumbnailUrl": "https://cdn.domain.tld/img/ipsum.jpg",
    "license": "http://creativecommons.org/licenses/by-sa/4.0/",
    "author": {
        "@type": "Person",
        "givenName": "John",
        "familyName": "Smith",
        "image": {
            "@type": "ImageObject",
            "url": "https://cdn.domain.tld/img/JohnSmith.jpg",
            "width": 128,
            "height": 128
        }
    },
    "publisher": {
        "@type": "Organization",
        "name": "ACME Inc.",
        "address": {},
        "logo": {},
        "url": "http://www.acme.org"
    },
    "commentCount": 1,
    "comment": [{
        "@type": "Comment",
        "upvoteCount": 7,
        "downcoteCount": 3,
        "author": {},
        "dateCreated": "2017-08-14T16:04:00",
        "text": "..."
    }]
}
```

```html
<!--
    Note that there can be more than one template for an itemtype, which is why
    the "data-template" attribute is necessary. Also, it is up to the template
    to determine which template to use for any properties in data that are,
    themselves, objects.

    You will also notice that values are not always intended to be used as node
    values. Sometimes, they are the URL for links. Other times, they need to be
    represented as both content and attribute, such as for `<time datetime=""></time>`.
    In such cases, additional attributes using dataset may be necessary in order
    to have the same data formatted in different ways.
-->
<template id="article-template">
    <article itemtype="http://schema.org/Article">
        <meta itemprop="wordCount" content="" />
        <header>
            <h1 itemprop="headline"></h1>
            By <div itemprop="author" itemtype="http://schema.org/Person" data-template="#author-template"></div>
            on <time datetime="" itemprop="datePublished" data-date-format="Y-m-d"></time>
        </header>
        <!-- Will be cloned for each section of article -->
        <section itemprop="articleSection"></section>
        <footer>
            <div itemprop="comment" data-tempalte="comment-tempalte"></div>
        </footer>
    </article>
</tempalte>
<tempalte id="author-tempalte">
    <div itemtype="http://schema.org/Person">
        <img src="" itemprop="image" class="round shadow author-img" />
        <span itemprop="givenName"></span>
        <span itemprop="familyName"></span>
        <!-- "sameAs" is often used for social links -->
        <a href="" itemprop="sameAs">
            <svg width="32" height="32"><use xlink:href="//cdn.domain.tld/img/icons.svg#twitter" /></svg>
        </a>
    </div>
</template>
<template id="comment-tempalte">
    <div itemtype="http://schema.org/Comment">
        By <span itemprop="author" data-tempalte="person-tempalte"></span>
        On <time datetime="" itemprop="dateCreated"></time>
        <blockquote itemprop="text"></blockquote>
    </div>
</template>
```

## Submitting Complex Objects

Submitting data will typically involve properties which are, themselves, objects.
It is also likely that a particular property may have multiple valid types that
it would accept (i.e. [Person](https://schema.org/Person)/homeLocation).

For this reason, structuring forms such that the data they submit is similar in
structure to the JSON-LD representaion is necessary.

```html
<form action="/Person" method="POST">
    <fieldset>
        <legend>Name</legend>
        <!-- Typical, simple data width @type & @context as hidden inputs -->
        <input type="hidden" name="@type" value="Person" />
        <input type="hidden" name="@context" value="http://schema.org" />
        <input type="text" name="givenName" required />
        <input type="text" name="familyName" />
    </fieldset>
    <fieldset>
        <legend>Home Address</legend>
        <input type="hidden" name="address[@type]" value="PostalAddress" />
        <input type="text" name="address[streetAddress]" />
        <!-- ... Address Data ... -->
    </fieldset>
    <!-- Multiple values -->
    <input type="url" name="sameAs[]" placeholder="Twitter URL" />
    <br />
    <input type="url" name="sameAs[]" placeholder="GitHub profile URL" />
    <!-- Multiple values of varying types -->
    <input type="url" name="image[]" placeholder="Gravatar URL" />
    <fieldset>
        <!-- Need to determine handling of uploads, but good example -->
        <input type="hidden" name="image[][@type]" value="ImageObject" />
        <input type="file" name="image[][$file?]" />
        <textarea name="image[][caption]"></textarea>
        <!-- ... -->
    </fieldset>
    <button type="submit">Submit</button>
</form>
```

## Collections and Lists

Any `GET` request to a resouce type [`GET /{Thing}`] without an identifier **MUST**
return an [`ItemList`](https://schema.org/ItemList) containing an array of
[`ListItem`s](https://schema.org/ListItem). Pagination is strongly encouraged.

### Requests

- `GET /{:Thing}[?[{(name|id|...)=:query}+][&limit=:limit][&offset=:offset]]`
- `GET /SoftwareApplication`
- `GET /Person?givenName=Smith&limit=10&offset=20`

### Example Response

```json
{
    "@type": "ItemList",
    "@context": "http://schema.org",
    "numberOfItems": 2,
    "itemListOrder": "Ascending",
    "itemListElement": [
        {
            "@type": "ListItem",
            "item": {
                "@type": "Thing",
                "name": "Thing 1",
                "url": "https://api.domain.tld/Thing/?item=1"
            },
            "position": 1,
            "nextItem": "https://api.domain.tld/Thing/?item=2"
        },
        {
            "@type": "ListItem",
            "item": {
                "@type": "Thing",
                "name": "Thing 2",
                "url": "https://api.domain.tld/Thing/?item=2"
            },
            "position": 2,
            "previousItem": "https://api.domain.tld/Thing/?item=1"
        }
    ]
}
```
