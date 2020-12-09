Here is my NGINX configuration, you might need `nginx-extras` to make it work.
Thanks to Will Norris [for the original work](https://willnorris.com/2014/07/webfinger-with-static-files-nginx/).

```nginx
        root /var/www/.well-known;

        location = /.well-known/webfinger {
          if ($request_method !~ ^(GET|HEAD)$) { return 405; }
          set_by_lua $resource 'return ngx.unescape_uri(ngx.req.get_uri_args()["resource"])';
          if ($resource = "") { return 400; }
          if ($resource = "acct:alexis@notmyidea.org")   { rewrite .* /webfinger.json last; }
          if ($resource = "alexis@notmyidea.org")        { rewrite .* /webfinger.json last; }
          if ($resource = "https://notmyidea.org")       { rewrite .* /webfinger.json last; }
        }

        location = /webfinger.json {
          types { application/jrd+json json; }
          add_header Access-Control-Allow-Origin "*";
        }
```
