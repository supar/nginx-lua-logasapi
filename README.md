# Convert request parameters to rest path for the prometheus

Use with nginx-lua-prometheus.
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
