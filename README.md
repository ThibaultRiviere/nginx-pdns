# NGINX PDNS

NGINX and PowerDNS running on Docker with MySQL backend.


### Build and start services

```
docker-compose stop
docker-compose rm
docker-compose build
docker-compose up
```


## DNS setup

See [here](https://n40lab.wordpress.com/2015/05/16/centos-7-using-the-powerdns-web-api-to-add-and-edit-records) for more information. 

### Create the first domain

```sh
export DOMAIN=mondomain.com
export IP=127.0.0.1
curl -X POST --data '{"name":"$DOMAIN", "kind": "Native", "masters": [], "nameservers": ["ns1.$DOMAIN", "ns2.$DOMAIN"]}' -v -H 'X-API-Key: changeme' $IP:8001/servers/localhost/zones
curl -X PATCH --data '{"rrsets": [ {"name": "$DOMAIN", "type": "A", "changetype": "REPLACE", "records": [ {"content": "$IP", "disabled": false, "name": "$DOMAIN", "ttl": 86400, "type": "A", "priority": 0 } ] } ] }' -H 'X-API-Key: changeme' $IP:8001/servers/localhost/zones/$DOMAIN
```

### Create a subdomain
```sh
export DOMAIN=mondomain.com
export SUBDOMAIN=s3.mondomain.com
export IP=127.0.0.1
curl -X PATCH --data '{"rrsets": [ {"name": "$SUBDOMAIN", "type": "CNAME", "changetype": "REPLACE", "records": [ {"content": "$DOMAIN", "disabled": false, "name":"$SUBDOMAIN", "ttl": 86400, "type": "CNAME", "priority": 0 } ] } ] }' -H 'X-API-Key: changeme' $IP:8001/servers/localhost/zones/$DOMAIN
```



### Dig the domain

```
dig example.local @$IP
```


## NGINX Configuration

All the file in the `nginx/conf` with the extension `.conf` will be load
See configuration [here](https://www.nginx.com/resources/wiki/start/topics/examples/full/)

If you want to use https maybe you should take a look [here](https://gist.github.com/cecilemuller/a26737699a7e70a7093d4dc115915de8)
