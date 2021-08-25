# RESTful API

## Introduction

A **RESTful API** is an architectural style for an application program interface (API) that uses HTTP requests to access and use data.
That data can be used to GET, PUT, PATCH, POST and DELETE data types, which refers to the reading, updating, creating and deleting of operations concerning resources.

We try to follow a set of standards that we use across all our available endpoints.
This guarantees clear readability and maintainability for all our developers or anyone having to use our API services.

## Conventions

- Follow Mozilla's HTTP Methods docs RFC Standards for GET, PUT, PATCH, and DELETE methods as much as possible.
- Use plural nouns in endpoints since each endpoint usually describes a collection of resources.
- Avoid unnecessary deep nesting, prefer 2-3 levels max.

### Example

```shell
GET /books - Retrieves all existing books.
POST /books - Creates a new book.
PUT /books/:id - Updates an existing book completely.
PATCH /books/:id - Updates an existing book partially.
DELETE /books/:id - Deletes an existing book.
```

## HTTP Headers

HTTP headers let the client and the server pass additional information with an HTTP request or response.
An HTTP header consists of its case-insensitive name followed by a colon (:), then by its value.
Whitespace before the value is ignored.

### Custom headers

Sometimes we need to provide custom additional information that isn’t part of the default set of HTTP headers.
To make sure we create these in a consistent way we use the same casing for all custom headers; kebab-case combined with PascalCase although headers are case insensitive.

```shell
Shop-Name:virgin-club
```

### Notes

Custom proprietary headers have historically been used with an X- prefix,
but this convention was deprecated in June 2012 because of the inconveniences it caused when nonstandard fields became standard in RFC 6648.

## References

- [https://swift.org/documentation/api-design-guidelines/](https://swift.org/documentation/api-design-guidelines/)
- [https://searchapparchitecture.techtarget.com/definition/RESTful-API](https://searchapparchitecture.techtarget.com/definition/RESTful-API#:~:text=A%20RESTful%20API%20is%20an,deleting%20of%20operations%20concerning%20resources)
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
