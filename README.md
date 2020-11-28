# MWE of docker-compose healthcheck bug

## Context

Three services (base, intermediate, and top) with intermediate depending on base, and
top depending on intermediate (and optionally base through the `network_mode: service:base`).

intermediate has `network_mode: service:base` **and** depends_on condition
service_healthy with respect to base. We have configured intermediate to eventually
`exit 1` when its default command is run.

top had depends_on service_healthy for intermediate.

## Bug

The expectation is that since the intermediate container errors out `exit 1`, the top
container will **not** start because clearly the intermediate container is unhealthy.
However we see that the intermediate container does start.

The bug seems to only occur when the intermediate container has a `network_mode: service:base`.

## Testing

Run this command:

``` shell
docker-compose down && docker-compose run --rm top; docker-compose logs -t --no-color | sort -u -k 3
```
