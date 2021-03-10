# AWS CloudFront

## Serving a React app from S3
To serve a React app from S3 _without breaking react-router_, CloudFront must serve `index.html` as default document
**and** redirect HTTP 400, 403 and 404 to `/index.html` with code 200.
This can be configured in the "Error Pages" tab.

If you don't do this, CloudFront will bypass the router and return a 403/404 because it can't find `index.html` in the specified directory.

Note that a 403 caused by a WAF defined on the distribution will be handled in the same way.
I.e. if you redirect a 403 to `/index.html`, an access denied by the WAF _will_ return `index.html`.
Custom error pages essentially allow you to poke holes in the firewall.
