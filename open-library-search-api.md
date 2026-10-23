# Open Library Search API

## Endpoint name

search.json

## Description

Searches the Open Library catalog for books that match your search words and returns the results as JSON.

## Request

```json
GET https://openlibrary.org/search.json
```

## Request parameters

| **Name** | **Type** | **Required** | **Description**                                                                                                                                                                                  |
| -------- | -------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| fields   | string   | No           | Labels to return, separated by commas. fields=\* returns all labels for the book. If fields is omitted, a standard set of labels is returned. Labels that don't exist are ignored without error. |
| limit    | integer  | No           | The maximum number of books to return. If omitted, returns the first batch of matching books. Use page to return the next batch.                                                                 |
| page     | integer  | No           | Which page of results to return. Use with limit to step through long result lists.                                                                                                               |
| q        | string   | No           | Words to search for. If omitted, no books are returned, and no error is given.                                                                                                                   |

## Response fields

| **Name**          | **Type** | **Description**                                                           |
| ----------------- | -------- | ------------------------------------------------------------------------- |
| docs              | array    | List of matching books in this batch. See [Book fields](#book-fields).                    |
| documentation_url | string   | Link to the documentation for the Search API.                             |
| num_found         | integer  | Same value as numFound.                                                   |
| numFound          | integer  | Number of matching books in total.                                        |
| numFoundExact     | boolean  | Indicates if the number of found books is an exact number or an estimate. |
| offset            | integer  | Number of books skipped. Null if offset wasn't sent.                      |
| q                 | string   | Words that were searched for.                                             |
| start             | integer  | The position of the first book in this batch, counting from 0.            |

## Book fields

| **Name**             | **Type**         | **Description**                                                                                                                 |
| -------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| author_key           | array of strings | The Open Library IDs for the authors of the book. They are in the same order as the author names.                               |
| author_name          | array of strings | Names of the book's authors.                                                                                                    |
| cover_edition_key    | string           | ID of the edition whose cover is shown.                                                                                         |
| cover_height         | integer          | Height of the cover image in pixels.                                                                                            |
| cover_i              | integer          | ID of the cover image. To view the image, use `https://covers.openlibrary.org/b/id/{cover_i}-L.jpg`                             |
| cover_width          | integer          | Width of the cover image in pixels.                                                                                             |
| ebook_access         | string           | Identifies whether a book is available as an ebook and what kind of access it has. See [Ebook access values](#ebook-access-values) below.               |
| edition_count        | integer          | Number of editions of this work.                                                                                                |
| first_publish_year   | integer          | Four-digit year the book was first published.                                                                                   |
| has_fulltext         | boolean          | Indicates whether the full text of the book is available digitally for viewing or searching within the platform.                |
| ia                   | array of strings | Item identifiers of the book within the Internet Archive system.                                                                |
| ia_collection        | array of strings | The digital collection identifiers for the book on the Internet Archive where the digitized file is grouped.                    |
| id_librivox          | array of strings | IDs for this audiobook on the **LibriVox** (<https://librivox.org/>) website. Use them to link to the audio file there.         |
| id_project_gutenberg | array of strings | IDs for this book on the **Project Gutenberg** (<https://www.gutenberg.org/>) website. Use them to link to the free copy there. |
| id_standard_ebooks   | array of strings | IDs for this book on the **Standard Ebooks** (<https://standardebooks.org/>) website. Use them to link to the free copy there.  |
| key                  | string           | ID for this work on **Open Library**. Add it to <https://openlibrary.org> to get the book's page.                               |
| language             | array of strings | Three-character publication language abbreviation.                                                                              |
| lending_edition_s    | string           | Identifier for a book edition that is eligible for digital lending.                                                             |
| lending_identifier_s | string           | The Internet Archive ID of the copy used for lending.                                                                           |
| public_scan_b        | boolean          | Flag indicating whether the book's digital scan is completely open to the public without lending restrictions.                  |
| title                | string           | Title of the book.                                                                                                              |

This is a standard set of fields. The parameter fields=\* returns all available fields from Open Library for a book.

## Ebook access values

| **Value**     | **Meaning**                                                             |
| ------------- | ----------------------------------------------------------------------- |
| borrowable    | Can be borrowed for a limited time, like a library book.                |
| no_ebook      | No digital copy exists.                                                 |
| printdisabled | Only readers with a print disability (such as blindness) can access it. |
| public        | Free for anyone to read.                                                |
| unclassified  | A digital copy exists, but its access level hasn't been set.            |


## Sample request and response

### Request

```json
GET https://openlibrary.org/search.json?q=ann%20patchett&fields=title,author_name,first_publish_year&limit=2
```

This request searches for "Ann Patchett" and returns the first two matching books with the title, first publication year, and author name for those books.

**Note**: If you want to test the request, copy the text starting from **https://**.

### Response

```json
{
  "numFound": 123,
  "start": 0,
  "numFoundExact": true,
  "num_found": 123,
  "documentation_url": "https://openlibrary.org/dev/docs/api/search",
  "q": "ann patchett",
  "offset": null,
  "docs": [
    {
      "author_name": [
        "Ann Patchett"
      ],
      "first_publish_year": 2019,
      "title": "The Dutch House"
    },
    {
      "author_name": [
        "Ann Patchett"
      ],
      "first_publish_year": 2023,
      "title": "Tom Lake"
    }
  ]
}
```

## Errors and unexpected results

### Labels that don't exist are ignored

**Status code:** 200 OK

**Request**

```json
GET https://openlibrary.org/search.json?q=ann%20patchett&fields=title,plot_summary&limit=1
```

**Response**

```json
{
  "numFound": 123,
  "start": 0,
  "numFoundExact": true,
  "num_found": 123,
  "documentation_url": "https://openlibrary.org/dev/docs/api/search",
  "q": "ann patchett",
  "offset": null,
  "docs": [
    {
      "title": "The Dutch House"
    }
  ]
}
```

The request asked for the book title and plot summary:

- title is a valid field and is returned.
- plot_summary is not a valid field and is not returned.

### Query text is missing

**Status code:** 200 OK

**Request**

```json
GET https://openlibrary.org/search.json?fields=title,author_name&limit=1
```

**Response**

```json
{
  "numFound": 0,
  "start": 0,
  "numFoundExact": true,
  "num_found": 0,
  "documentation_url": "https://openlibrary.org/dev/docs/api/search",
  "q": "",
  "offset": null,
  "docs": []
}
```

All response labels come back, but numFound is 0 and docs is empty. No error is returned.

### Limit field is entered as a string

**Status code:** 422 Unprocessable Content

**Request**

```json
GET https://openlibrary.org/search.json?q=ann%20patchett&fields=title,author_name&limit=two
```

**Response**

```json
{
  "detail": [
    {
      "type": "int_parsing",
      "loc": [
        "query",
        "limit"
      ],
      "msg": "Input should be a valid integer, unable to parse string as an integer",
      "input": "two"
    }
  ]
}
```

The request fails and returns an error message indicating that limit must be an integer, not a string.

### Error response fields

| **Name** | **Type** | **Description** |
| --- | --- | --- |
| detail | array | List of errors in the request. Each item describes one error. |
| detail.type | string | Code for the kind of error, such as int_parsing. |
| detail.loc | array of strings | Where the error is: the part of the request (query) and the parameter name (limit). |
| detail.msg | string | Text of the error message. |
| detail.input | string | The value that contains the error. |
