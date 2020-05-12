# Convert request parameters to rest path for the prometheus

Use with nginx-lua-prometheus.

Create /etc/nginx/prometheus.conf

```
lua_shared_dict prometheus_metrics 10M;
init_by_lua '
    prometheus = require("prometheus").init("prometheus_metrics")
    
    metric_requests = prometheus:counter(
      "nginx_http_requests_total", "Number of HTTP requests", {"host", "method", "status", "api"})
    metric_request_sizes = prometheus:counter(
      "nginx_http_request_size_bytes", "The HTTP request sizes in bytes", {"host", "method", "api"})
    metric_latency = prometheus:histogram(
      "nginx_http_request_duration_seconds", "HTTP request latency", {"host", "api"})
    metric_connections = prometheus:gauge(
      "nginx_http_connections", "Number of HTTP connections", {"state"})
';
```

Include /etc/nginx/prometheus.conf in the http section

```
http {
...
    include /etc/nginx/prometheus.conf;
}
```

If request parameters are structured You may convert them to rest path representation and use for the metrics 

For example web application request

```
[GET | POST] => {
    'param1' => 'module_a',
    'param2' => 'command_a1'
    ....
}
```

In the nginx virtual host define variable

```
server {
    ...
    set $log_metrics_params_to_api "param1/param2";
    include /etc/nginx/log_by_lua.conf;
}
```

Or write metrics for the normal rest

```
server {
    ...
    include /etc/nginx/log_by_lua.conf;

    location ... {
        set $log_metrics_rest_to_api $uri;
    }
}
```

#### Using map

```
map $uri $log_metrics_rest_to_api {
    default '';

    # group requests
    ~^/api/items*    '/api/items';
}

server {
    ...
    location ... {
        include /etc/nginx/log_by_lua.conf;
    }
}
```
