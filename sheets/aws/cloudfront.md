# AWS CloudFront

## Serving a React app from S3
To serve a React app from S3 _without breaking react-router_, CloudFront must serve `index.html` as default document
**and** redirect HTTP 400, 403 and 404 to `/index.html` with code 200.
This can be configured in the "Error Pages" tab.

If you don't do this, CloudFront will bypass the router and return a 403/404 because it can't find `index.html` in the specified directory.
