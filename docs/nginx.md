# Nginx web server notes

### Replace content in the backend response

```
location /admin/ {
    proxy_pass http://localhost:8080/;
    proxy_set_header Accept-Encoding "";
    sub_filter "http://your_server/" "http://your_server/admin/";
    sub_filter_once off;
}
```