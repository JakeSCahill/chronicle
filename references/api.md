# API

**getTrytes**

Use the transaction hash to get the signature message fragment

```
curl http://host:port/api \
-X POST \
-H 'Content-Type: application/json' \
-H 'X-IOTA-API-Version: 1' \
-d '{
"command": "getTrytes",
"hashes": [
  "TRANSACTION_HASH_1","TRANSACTION_HASH_N"
  ]
}'
```
**findTransactions** by addresses

Find transactions using one or more addresses
```
curl http://host:port/api \
-X POST \
-H 'Content-Type: application/json' \
-H 'X-IOTA-API-Version: 1' \
-d '{
"command": "findTransactions",
"addresses": [
  "ADDRESS_1","ADDRESS_N"
  ]
}'
```
**findTransactions** by bundle hashes

Find transactions using one or more bundle hashes
```
curl http://host:port/api \
-X POST \
-H 'Content-Type: application/json' \
-H 'X-IOTA-API-Version: 1' \
-d '{
"command": "findTransactions",
"bundles": [
  "BUNDLE_HASH_1", "BUNDLE_HASH_N"
  ]
}'
```
**findTransactions** by hashes

Find transactions using one or more hashes
```
curl http://host:port/api \
-X POST \
-H 'Content-Type: application/json' \
-H 'X-IOTA-API-Version: 1' \
-d '{
"command": "findTransactions",
"approvees": [
  "HASH_1", "HASH_N"
  ]
}'
```
**findTransactions** by tags

Find transactions with the same tag.  The expected response is tags hints
```
curl http://host:port/api \
-X POST \
-H 'Content-Type: application/json' \
-H 'X-IOTA-API-Version: 1' \
-d '{
"command": "findTransactions",
"tags": [
  "TAG_1", "TAG_N"
  ]
}'
```
**findTransactions** by tag hint

Find transactions using the IOTA Area Code (IAC).  The minimum required tag length is 4-trytes to search by IAC
```
curl http://host:port/api \
-X POST \
-H 'Content-Type: application/json' \
-H 'X-IOTA-API-Version: 1' \
-d '{
"command": "findTransactions",
"hints": [
  {"tag":"999999999999999999999999999","month":8,"year":2019, "page_size": 500}
  ]
}'
```
**findTransactions** by address hint

Find transactions by address hint
```
curl http://host:port/api \
-X POST \
-H 'Content-Type: application/json' \
-H 'X-IOTA-API-Version: 1' \
-d '{
"command": "findTransactions",
"hints": [
  {"address":"JPYUAV9MBDZG9ZX9BAPBBMYFEVORNBIOOZCYPZDZNRGKQYT9HFEXXXBG9TULULJIOWJWQMXSPLILOJGJG","month":8,"year":2019, "page_size": 500}
  ]
}'
```

