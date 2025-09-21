# 410 Gone Container

A container that returns HTTP 410 Gone response for all HTTP requests.

Used for service termination such as SOS24 (created because Traefik, which we use, does not provide a solution to return 410 gone by itself).

```bash
docker run -p 80:80 ghcr.io/masebb/410-gone:latest
```

You can customize the organization and service names with environment variables:

```bash
docker run -p 80:80 \
  -e ORG_NAME="なんとか実行委員会" \
  -e SERVICE_NAME="なんとか実行委員会オンラインシステム(2024年度版)" \
  -e ORG_LINK="https://example.com" \
  ghcr.io/masebb/410-gone:latest
```
