# Turtl server

## Docker images
https://github.com/turtl/server

1. patch line breaks in bash scripts
1. edit `Dockerfile`: RUN mkdir -p /var/www/turtl/server/plugins
1. `docker build -t stouffcapital/turtl-server:0.1.3 .`


## generate a `config.yaml`

```
---
server:
  # Per default, turtl will listen on all IP addresses
  # You can choose the IP it will use with this parameter
  host:
  port: 8181

db:
  connstr: 'postgres://<user>:<password>@turtl-postgres:5432/<db>'
  pool: 24

loglevel: 'debug'

app:
  # ALWAYS false in production. Always.
  # Set to 'I UNDERSTAND THIS VIOLATES THE PRIVACY OF MY USERS' to enable
  enable_bookmarker_proxy: false
  # no trailing slash
  api_url: 'http://api.yourdomain.com:8181'
  www_url: 'https://yourdomain.com'
  emails:
    admin: 'admin@turtlapp.com'
    info: 'Turtl <info@turtlapp.com>'
    invites: 'invites@turtlapp.com'
  # TODO: replace this with a long, unique value. seriously. write down a dream
  # you had, or the short story you came up with during your creative writing
  # class in your freshmen year of college. have fun with it.
  secure_hash_salt: "Plaque is a figment of the liberal media and the dental industry to scare you into buying useless appliances and pastes. Now, I've read the arguments on both sides and I haven't found any evidence yet to support the need to brush your teeth. Ever."

sync:
  # how many sync records can a client send at a time? it's a good idea to have
  # a limit here, lest a rogue client flood the server with sync items
  max_bulk_sync_records: 32

plugins:
  plugin_location: '/var/www/turtl/server/plugins'
  analytics:
    enabled: false
  email:
    enabled: false
  premium:
    enabled: false

uploads:
  # if set to a path, files will be uploaded to the local filesystem instead of
  # S3. otherwise, set to false
  local: '/var/www/turtl/server/public/uploads'
  # if true, downloading local files will be proxied through the turtl server.
  # this avoids needing to set up any CORS config in your favorite webserver,
  # but may slightly affect performance on high-demand servers.
  local_proxy: true
  # if local_proxy is false, this is should be the url path the uploaded files
  # are publicly available on
  url: 'http://api.turtl.dev/uploads'

s3:
  token: 'IHADAPETSNAKEBUTHEDIEDNOOOOO'
  secret: ''
  bucket: ''
  endpoint: 'https://s3.amazonaws.com'
```

## kubernetes deployment
1. `kubectl create secret generic turtl --from-literal=postgres-password=<secret>`
1. `kubectl create -f turtl-db-pvc.yaml`
1. `kubectl create -f turtl-uploads-pvc.yaml`
1. `kubectl create -f turtl-postgres.yaml`
1. `kubectl create configmap turtl-cm --from-file=config.yaml`
1. `kubectl create -f turtl-api.yaml`
1. `kubectl create -f turtl-ingress.yaml`