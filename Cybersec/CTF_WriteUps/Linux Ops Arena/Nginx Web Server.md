```
nginx -s signal 

```
```
nginx -t
```
- `stop` — fast shutdown
- `quit` — graceful shutdown
- `reload` — reloading the configuration file
- `reopen` — reopening the log files
https://nginx.org/en/docs/beginners_guide.html#control

Config locations:

- nginx: `/etc/nginx/nginx.conf`, extra sites in `/etc/nginx/conf.d/*.conf`
- httpd: `/etc/httpd/conf/httpd.conf`, extra config in `/etc/httpd/conf.d/*.conf` 

Content served from `/usr/share/nginx/html` (nginx) or `/var/www/html` (httpd) by default.

More than one site 
```
server{
	server_name:

}

server {
	server_name
}
```

one site multiple port
```
server {
    listen 80;
    listen 8080;

    server_name example.com;
    root /data/site;
}
```
