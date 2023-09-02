# MySQL

Execute `docker compose up -d`

- Uncomment Line 20 of `docker-compose.yml` and run it if you want to load the initial data.

---

# If you want to update test-db

1. Execute `git submodule update --init --recursive` for cloning `test-db`.
2. Check `test-db/README.md`
3. update `test-db-updated` files or docker-compose.yml


# OR If you want to import using DataGrip


<img width="725" alt="image" src="https://github.com/KimDoubleB/LAB/assets/37873745/b95a38f7-017b-41ca-ac57-17b075287f48">

- Use `Run SQL Script`


<img width="554" alt="image" src="https://github.com/KimDoubleB/LAB/assets/37873745/8fe62cd3-a9dd-425d-bc74-b9a9203bf246">

- Select DDL script with dump files