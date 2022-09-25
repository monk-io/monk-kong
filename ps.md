curl --location --request POST 'http://localhost:8001/services/' \                                                                                                             
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "admin-api",
    "host": "localhost",
    "port": 8001
}'


curl --location --request POST 'http://localhost:8001/services/{service_id|service_name}/routes' \                                                                                 
--header 'Content-Type: application/json' \
--data-raw '{
    "paths": ["/admin-api"]
}'


curl --location --request POST 'http://localhost:8001/services/5f9a47fb-4308-4585-866f-b618cd76a838/routes' \                                                                  
--header 'Content-Type: application/json' \
--data-raw '{
    "paths": ["/admin-api"]
}'