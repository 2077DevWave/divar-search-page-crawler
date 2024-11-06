# Divar Cloudflare Worker

This Cloudflare Worker allows users to retrieve a list of URLs from Divar based on specific search criteria such as city, query, and pagination parameters. Users can retrieve up to 50 pages in a single request, with the ability to continue pagination from a specified timestamp for results beyond 50 pages.

## Usage

### Endpoint
The worker endpoint is accessible as a `GET` request to: https://divar-crawler.sswsuport.workers.dev/


### Query Parameters
- **`city_id`** (optional): The ID of the city to search within. Defaults to `"1"` if not provided.
- **`query`** (required): The term to search for, such as `"207"` for vehicle searches.
- **`page`** (required): The number of pages to crawl (up to a maximum of 50 per request).
- **`lastPostDate`** (optional): ISO-8601 timestamp to indicate the starting point for pagination if the user is requesting beyond the first 50 pages. If not provided for the first request, it defaults to the current date and time.

### Example Requests
#### Basic Request (up to 50 pages)
```
https://divar-crawler.sswsuport.workers.dev/?city_id=38&query=207&page=3
```

#### Request for Pages Beyond 50
To retrieve additional pages beyond the first 50, set `page=50` in the initial request to get the `lastPostDate` from the response. Then, use this `lastPostDate` in the next request:
```
https://divar-crawler.sswsuport.workers.dev/?city_id=38&query=207&page=50&lastPostDate=2024-11-01T08:00:00.123456Z
```

### Response
The response returns JSON data with:
- **`urls`**: An array of URLs based on the search results.
- **`lastPostDate`**: The timestamp of the last post, which can be used in subsequent requests for continued pagination.

#### Example Response
```json
{
  "urls": [
    "https://divar.ir/v/پژو-207i-دنده‌ای-TU3،-مدل-۱۴۰۲/wZiY0NfM",
    "https://divar.ir/v/پژو-206-مدل-۱۴۰۱/abcd1234"
  ],
  "lastPostDate": "2024-11-01T08:00:00.123456Z"
}
```

### Notes
- If no `lastPostDate` is provided in the initial request, the current date and time are used as the starting point.
- To fetch results beyond the first 50 pages, a new request with the `lastPostDate` parameter is required.

## Error Handling
- The API returns a `400` status with an error message if `query` or `page` is missing or if `page` is not a positive integer.
- In case of an API failure, an error response will be returned with a `500` status and an error message.
