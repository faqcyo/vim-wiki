[Go back to PortSwigger Index](index)

# Web Cache Deception / Poisoning

Akamai and Fastly interpret the %00 (null character) as part of the URL. An OpenLiteSpeed server interprets the %00 as a delimiter

Delimiters test (with the endpoint `/settings/users/list` as an example):
1. Modify `/settings/users/list` to `/settings/users/listxxx` and compare the responses for future reference. (Note that if the response is equal, this means that the request is being redirected. You'll need to choose a different endpoint to test)
2. Then add `;` as a delimiter on the URL: `/settings/users/list;xxx`
  - If the response is identical to the response of `/settings/users/list`, then the `;` is used as a delimiter and the origin server interprets the path as `/settings/users/list`
  - If the response is identical to the response of `/settings/users/listxxx`, then the `;` isn't used as a delimiter and the origin server interprets the path as `/settings/users/list;xxx`
3. Once you've identified the delimiters used by the origin server, test whether they're also used by the cache by adding a static file extension to the end of the path. If the response is cached then:
  - The cache might not be using the delimiter and interprets the full URL path with the static extension
  - There is a cache rule to store responses for requests ending in the file extension used (e.g. .js).

## Delimiter encoding discrepancies

Consider the following scenario; if we make a request to `/profile%23wcd.css`, the origin server decodes the %23 as # and returns the profile information because it uses # as a delimiter. The cache also uses the # as delimiter but does not decode %23, so it interprets the path as `/profile%23wcd.css`.

In the previous scenario we had a discrepancy between the origin server and the cache. 
