# Loki autoscaling cluster with Etcd and Minio in Docker Swarm

Inside the docker swarm

Start the loki cluster

```
cd ./lokiCluster/loki
docker stack deploy -c docker-compose.yaml loki
cd ..
```

Attach HAproxy as ingress 

```
cd ./lokiCluster/haproxy
docker stack deploy -c docker-compose.yaml haproxy-ingress
```

_From your machine_

Don't forget to add an entry in the /etc/hosts file pointing to your swarm entrypoint for loki.local

eg:
```
10.10.10.5 loki.local
```
Use this as promtail endpoint

edit ./logGen/config/promtail.yaml
```
...
clients:
    - url: http://loki.local/loki/api/v1/push
      tenant_id: docker
...
```

run promtail 
```
cd ./logGen
docker-compose up
```


NOTE: multi-tenancy is enabled don't forget to include the header X-Scope-OrgID in your query (or grafana datasource). Or disable multi-tenancy in loki config:
```
auth_enabled: false
```

## Ref link
 - <https://community.grafana.com/t/loki-error-on-port-9095-error-contacting-scheduler/67263/2>
 - <https://github.com/grafana/loki/tree/main/production/docker/config>
 - <https://github.com/cniackz/loki/blob/main/production/loki.yaml>
 - <https://blog.min.io/how-to-grafana-loki-minio>
 - <https://github.com/guessi/docker-compose-etcd>
 - <https://raw.githubusercontent.com/guessi/docker-compose-etcd/master/docker-compose.yml>
 - <https://www.haproxy.com/blog/haproxy-on-docker-swarm-load-balancing-and-dns-service-discovery>

## Check links
Check your cluster status with:
  - <http://swarm-address/stats> (admin:password123)
  - <http://loki.local/distributor/ring>
  - <http://loki.local/config>
  - <http://loki.local/metrics>

