
https://stackoverflow.com/questions/60228723/cannot-connect-to-local-postgresql-db-using-dbeaver

Quando usar o JDBC, você tem que usar autenticação por senha. Nem `ident` ou `peer` irão funcionar.

Você terá que adicionar isso:

```
host    all             all             127.0.0.1/32           md5
```

No seu arquivo `pb_hba.conf`

(substitua `md5` com `scram-sha-256` se você estiver usando esse método criptográfico.)