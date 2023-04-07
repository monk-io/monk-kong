# Kong & Monk

This repository contains Monk.io template to deploy Kong Cluster either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

## Prerequisites

- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

## Make sure monkd is running

```bash
foo@bar:~$ monk status
daemon: ready
auth: logged in
not connected to cluster
```

## Clone Repository

```bash
git clone https://github.com/monk-io/kong
```

## Load Template

```bash
cd kong
monk load MANIFEST
```

```bash
foo@bar:~$ monk list kong
✔ Got the list
Type      Template    Repository  Version  Tags
runnable  kong/db     local       -        -
runnable  kong/kong   local       -        Networking, Cross-platform, Lua scripting, API Gateway, Microservices, Load Balancing, Containerization, Cloud-native, DevOps, Security, Analytics, Monitoring, Scalability, Performance, RESTful APIs, GraphQL, Service Mesh
group     kong/stack  local       -        Networking, Cross-platform, Lua scripting, API Gateway, Microservices, Load Balancing, Containerization, Cloud-native, DevOps, Security, Analytics, Monitoring, Scalability, Performance, RESTful APIs, GraphQL, Service Mesh
```

## Deploy Stack

```bash
foo@bar:~$ monk run kong/stack
? Select which 'kong/stack' to run local/kong/stack
✔ Starting the run job: local/kong/stack... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% kong:latest local
✔ [================================================] 100% pantsel/konga:latest local
✔ Checking/pulling images DONE
✔ Starting containers DONE
✔ New container 825849c4b2cc97bfc522e5731e8535fa-local-kong-db-postgres created DONE
✔ New container 657cd0636961ca4f91adbdafd1679d5e-local-kong-kong-konga created DONE
✔ New container ffabdc3c8d51b7bcab82a07c5ec4cb82-l-kong-kong-kong-migrations-up created DONE
✔ New container b94f2f8bdf3303f5aa34f0d12e9faefb-local-kong-kong-kong-app created DONE
✔ Started local/kong/stack
🔩 templates/local/kong/stack
 └─🧊 Peer local
    ├─🔩 templates/local/kong/db
    │  └─📦 825849c4b2cc97bfc522e5731e8535fa-local-kong-db-postgres running
    │     ├─🧩 postgres:latest
    │     └─💾 /var/lib/monkd/volumes/db_data -> /var/lib/postgresql/data
    ├─🔩 templates/local/kong/kong
    │  └─📦 b94f2f8bdf3303f5aa34f0d12e9faefb-local-kong-kong-kong-app running
    │     ├─🧩 kong:latest
    │     ├─🔌 open 1.1.1.1:8000 (0.0.0.0:8000) -> 8000
    │     └─🔌 open 1.1.1.1:8001 (0.0.0.0:8001) -> 8001
    ├─🔩 templates/local/kong/kong
    │  └─📦 ffabdc3c8d51b7bcab82a07c5ec4cb82-l-kong-kong-kong-migrations-up running
    │     └─🧩 kong:latest
    └─🔩 templates/local/kong/kong
       └─📦 657cd0636961ca4f91adbdafd1679d5e-local-kong-kong-konga running
          ├─🧩 pantsel/konga:latest
          └─🔌 open 1.1.1.1:1337 (0.0.0.0:1337) -> 1337

💡 You can inspect and manage your above stack with these commands:
        monk logs (-f) local/kong/stack - Inspect logs
        monk shell     local/kong/stack - Connect to the container's shell
        monk do        local/kong/stack/action_name - Run defined action (if exists)
💡 Check monk help for more!
```

## Create admin service for kong

```bash
$ curl --location --request POST 'http://13.49.125.144:8001/services/' \
--header 'Content-Type: application/json' \
--data-raw '{
   "name": "admin-api",
   "host": "13.49.125.144",
   "port": 8001
}'
```

## Create admin service route for konga

```bash
curl --location --request POST 'http://13.49.125.144:8001/services/<service_id>/routes' \
--header 'Content-Type: application/json' \
--data-raw '{
    "paths": ["/admin-api"]
}'
```

## Variables

The variables are in `stack.yml` file. You can quickly setup by editing the values here.

| Variable               | Description            | Default |
| ---------------------- | ---------------------- | ------- |
| kong_database_name     | Kong database name     | kong    |
| kong_database_password | Kong database password | kong    |
| kong_database_user     | Kong database user     | kong    |
| kong_database_port     | Kong database port     | 5432    |
| kong_proxy_listen      | Kong Proxy listen      | 8000    |
| kong_admin_listen      | Kong admin listen      | 8001    |
| konga_admin_listen     | Konga port             | 1337    |

## Stop, remove and clean up workloads and templates

```bash
monk purge -x -a
```
