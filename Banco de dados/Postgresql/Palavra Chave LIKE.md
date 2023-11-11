
Esta palavra chave é responsável por dar match em entidades com colunas que contenham um valor que esteja de acordo com o que é fornecido, sua estrutura básica é: 
```sql
select * 
	from cd.facilities 
	where
		name like '%Tennis%';
```

No exemplo acima estamos mostrando todas as colunas (denotadas pelo wildcard `*`) da table `cd.facilities`, e com a palavra chave `where` estamos filtrando quais entidades devem ser selecionadas, atenção especial ao filtro realizado:
```sql
	name like '%Tennis%';
```

Como podemos ver, estamos filtrando por todas as entidades na table desejada (esta que contém uma coluna chamada `name`), e além disso, o filtro somente está selecionando as entidade que possuem a palavra `%Tennis%`, como valor para a coluna `name`.
É necessário, porém, ter atenção especial com os caracteres `%` utilizados no começo e no final da palavra, eles não significam literalmente o sinal de porcentagem, mas sim, wildcards, abaixo segue uma explicação sobre o que são e o que representam.

>[!info]
># Operadores
>O operador `%` é responsável por dar match em qualquer string que existir em sua posição (ele funciona como um wildcard de strings).
>
>O operador `__` é responsável por dar match em qualquer caractere que existir em sua posição (wildcard de caractere).

Apesar de o Postgresql nos dar suporte para [[Expressões Regulares]], é válido ressaltar que o operador `like` é consideravelmente mais portável entre sistemas (Como mysql, por exemplo).
