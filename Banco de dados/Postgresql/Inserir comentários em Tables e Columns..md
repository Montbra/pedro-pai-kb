Quando precisar inserir comentários em suas tables ou em componentes de suas tables (para posterior compreensão por sua parte, bem como por parte de seus colegas) você pode utilizar os seguintes comandos SQL em seus scripts:

>[!note]
># Inserir comentários em Tables.
>Utilize o comando `COMMENT ON TABLE <nome_da_tabela> IS '<comentário>';`

Por exemplo:

```sql
COMMENT ON TABLE clientes IS 'Esta tabela armazena informações sobre os clientes.';
```

>[!note]
># Inserir comentários em Columns.
>Utilize o comando 
>`COMMENT ON COLUMN <nome_da_tabela>.<nome_da_coluna> IS '<comentário>';`

Por exemplo:

```sql
COMMENT ON COLUMN clientes.nome IS 'O nome do cliente.';
```
