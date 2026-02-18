# Afrimentorship
Docker Assignment
 ShopNow — Stale product inventory due to caching

What I solved

Issue: Product details page shows incorrect inventory after stock updates.
Root cause: Cache keys don’t change when inventory changes (TTL-only caching), so reads can return stale cached entries.
Fix: Versioned cache keys (or event-driven invalidation). On inventory update, increment version so the read path automatically uses a new key.
shopnow/
  app/
    main.py
    db.py
    cache.py
    requirements.txt
  Dockerfile
  docker-compose.yml
  .env
  README.md
x 
Ijeomas-MBP:shopnow ijeomaojiako$ docker build --no-cache -t shopnow-api .
                                                                 0.0s
$ ls -la app
total 32
drwxr-xr-x@ 6 ijeomaojiako  staff   192 Feb 17 18:05 .
drwxr-xr-x@ 6 ijeomaojiako  staff   192 Feb 17 18:09 ..
-rw-r--r--@ 1 ijeomaojiako  staff   284 Feb 17 17:30 cache.py
-rw-r--r--@ 1 ijeomaojiako  staff   278 Feb 17 16:55 db.py
-rw-r--r--@ 1 ijeomaojiako  staff  2524 Feb 17 16:53 main.py
-rw-r--r--@ 1 ijeomaojiako  staff   119 Feb 17 18:05 requirements.txt
 docker compose up --build -d
                                                                   0.0s
curl -fsS http://localhost:8000/health
{"status":"ok"}
o$ curl -fsS -X POST "http://localhost:8000/products/1/inventory?inventory=10"
{"ok":true,"id":1,"inventory":10}
$ curl -fsS "http://localhost:8000/products/1"
{"id":1,"name":"Product 1","inventory":10,"updated_at":"2026-02-18 00:26:18.958547"}
 curl -fsS -X POST "http://localhost:8000/products/1/inventory?inventory=7"
curl -fsS http://localhost:8000/products/1
{"ok":true,"id":1,"inventory":7}
curl -fsS http://localhost:8000/products/1
{"id":1,"name":"Product 1","inventory":7,"updated_at":"2026-02-18 00:31:03.652315"}
This command curl -fsS http://localhost:8000/health is used to confirm that the API is reachable.
Cache staleness test check command
curl -fsS -X POST "http://localhost:8000/products/1/inventory?inventory=7"
curl -fsS http://localhost:8000/products/1
