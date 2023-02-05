# Minio & Monk
This repository contains Monk.io template to deploy Minio Cluster either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

# Prerequisites
- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

#### Make sure monkd is running.
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


#### Let's take a look at the themes I have installed.
```bash
foo@bar:~$ monk list kong
✔ Got the list
Type      Template          Repository  Version  Tags
runnable  kong/db      local       -        -
runnable  kong/stack   local       -        -
runnable  kong/kong    local       -        -
runnable  monk-minio/stack  local       -        -

```

## Deploy Stack
```bash
foo@bar:~$ monk run kong/stack
✔ Starting the job: templates/local/kong/stack... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% postgres:latest monk-1
✔ [================================================] 100% kong:latest monk-2
✔ [================================================] 100% pantsel/konga:latest monk-2
✔ Checking/pulling images DONE
✔ Updating containers DONE
✔ ✨ templates/local/kong/stack updated successfully

🔩 templates/local/kong/stack
 ├─🧊 Peer monk-1
 │  └─🔩 templates/local/kong/db
 │     └─📦 bb027a62a9e198e7db5c8700b2d3449c-local-kong-db-postgres
 │        ├─🧩 postgres:latest
 │        ├─💾 /var/lib/monkd/volumes/db_data -> /var/lib/postgresql/data
 │        └─🔌 open 13.48.129.155:5432 (0.0.0.0:5432) -> 5432
 └─🧊 Peer monk-2
    ├─🔩 templates/local/kong/kong
    │  └─📦 a189131be0f9204e756cac22e1f63e2e-ocal-kong-kong-konga
    │     ├─🧩 pantsel/konga:latest
    │     └─🔌 open 13.49.125.144:1337 (0.0.0.0:1337) -> 1337
    ├─🔩 templates/local/kong/kong
    │  └─📦 e69404c5da9ed137ebb5e6056814806a-k-kong-kong-kong-migrations-up
    │     └─🧩 kong:latest
    └─🔩 templates/local/kong/kong
       └─📦 bfb8d97b8d72f64e51e0ccdc2818c204-l-kong-kong-kong-app
          ├─🧩 kong:latest
          ├─🔌 open 13.49.125.144:8001 (0.0.0.0:8001) -> 8001
          ├─🔌 open 13.49.125.144:8000 (0.0.0.0:8000) -> 8000
          ├─🔌 open 13.49.125.144:8444 (0.0.0.0:8444) -> 8444
          └─🔌 open 13.49.125.144:8443 (0.0.0.0:8443) -> 8443

💡 You can inspect and manage your above stack with these commands:
	monk logs (-f) kong/stack - Inspect logs
	monk shell     kong/stack - Connect to the container's shell
	monk do        kong/stack/action_name - Run defined action (if exists)
💡 Check monk help for more!
```
 
 ## Create admin service for konga
 ```
 curl --location --request POST 'http://13.49.125.144:8001/services/' \                                                                                                             
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "admin-api",
    "host": "13.49.125.144",
    "port": 8001
}'
```

## Create admin service route for konga
```
curl --location --request POST 'http://13.49.125.144:8001/services/<service_id>/routes' \                                                                  
--header 'Content-Type: application/json' \
--data-raw '{
    "paths": ["/admin-api"]
}'
```


## Variables
The variables are in `stack.yml` file. You can quickly setup by editing the values here.

| Variable                     	| Description                               	|
|------------------------------	|-------------------------------------------	|
| kong_database_name            | Kong database name, Default: kong 	               |
| kong_database_password        | Kong database password, Default kong                     	|
| kong_database_user            | Kong database user, Default: kong                     	|
| kong_database_port            | Kong database port, Default: 5432                     	|
| kong_proxy_listen             | Kong Proxy listen, Default 8000                     	|
| kong_admin_listen               | Kong admin listen, Default 8001                     	|
| konga_admin_listen               | Konga port Default: 1337                     	|



## Stop, remove and clean up workloads and templates

```bash
monk purge -x -a
```