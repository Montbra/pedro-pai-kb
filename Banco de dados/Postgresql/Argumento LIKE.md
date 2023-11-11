
Esta palavra chave é responsável por dar match em entidades com colunas que contenham um valor que esteja de acordo com o que é fornecido, sua estrutura básica é: 
```sql
select * 
	from cd.facilities 
	where
		name like '%Tennis%';
```

No exemplo acima estamos mostrando todas as colunas (denotadas pelo wildcard `*`) da table `cd.facilities`, e com a palavra chave `where` 

>[!info]
># Operadores
>O operador `%` é responsável por dar match em qualquer string que existir em sua posição (ele funciona como um wildcard de strings) .
>
>O operador `__` é responsável por dar match em qualquer caractere que existir em sua posição (wildcard de caractere).

Apesar de o Postgresql nos dar suporte para [[Expressões Regulares]], é válido ressaltar que o operador `like` é consideravelmente mais portável entre sistemas (Como mysql, por exemplo).
