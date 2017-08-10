# api-specs
Specifications for the microdata API (unnamed)

## Index
- [Contributing](./docs/CONTRIBUTING.md)
- [Relevant Resources](#relevant-resources)
- [Examples](#examples)
- [Advanced Examples](#advanced-example)

> This is a non-code repository for describing the structure and functionality
> of the API, as well as how mapping data to content will take place.

## Relevant Resources
- [Schema.org](https://schema.org)
- [`<template>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template)

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
    `data-import` specifies both a data source, as well as a templte to use for
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

- HTML using `<link rel="import">`
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
    "articleSection": [],
    "thumbnailUrl": "https://cdn.domain.tld/img/ipsum.jpg",
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
    "comment": [{}]
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
            By <div itemprop="author" itemtype="http://schema.org/Person" data-tempalte="#author-tempalte"></div>
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
