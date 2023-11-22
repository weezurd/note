**Expose services behind VPN using API Gateway**
- Because requests to API Gateway are HTTPS and will be TLS terminated, listener have to listen on TCP 80.

**Redirect HTTP to HTTPS for services using API Gateway**
- Setup a Cloudfront distribution:
    - Origin: API gateway's api, the *.execute-api.* one.
    - Viewer protocol policy: `Redirect HTTP to HTTPS`.
    - Allowed HTTP methods: `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`
    - Cache key and origin requests: choose the ones that are recommended for API Gateway.
- In Route53, create a record which alias the main domain to Distribution domain name.
