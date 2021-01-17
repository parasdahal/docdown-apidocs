# Errors

The Docdown API uses the following error codes:

| Error Code | Meaning                                                                                   |
| ---------- | ----------------------------------------------------------------------------------------- |
| 400        | Bad Request -- Your request is invalid or validation failed.                              |
| 401        | Unauthorized -- Your API key is incorrect.                                                |
|            |
| 404        | Not Found -- The specified item could not be found.                                       |
|            |
| 406        | Not Acceptable -- You requested a format that isn't acceptable.                           |  |
| 426        | Upgrade Required -- Your subscription is invalid or usage is over limit.                  |
| 429        | Too Many Requests -- Slow down!                                                           |
| 500        | Internal Server Error -- We had a problem with our server. Try again later.               |
| 503        | Service Unavailable -- We're temporarily offline for maintenance. Please try again later. |

The Docdown API rate limit is 10 requests per 10 seconds.
