## Oscar - Exercícios MongoDB

## Nível 1 -

1.1 Quantos registros existem na coleção de indicados ao Oscar?

R: 11104
```
db.indicados.countDocuments() 
```

1.2 Quais são as diferentes categorias de premiação que existem no banco de dados? Liste todas as categorias únicas.

R: 122 registros 
```
db.indicados.distinct("categoria").length();
db.indicados.distinct("categoria")();
```
1.3 Qual foi o primeiro ano de cerimônia do Oscar registrado na base?

R: 1928
```
{ano_cerimonia: {$ne: null}}
```
1.4 Qual foi o último ano de cerimônia registrado na base?

R:2026
```
{"cerimonia":-1}
```
1.5 Quantas cerimônias do Oscar estão registradas no total?

R: 98 
```
db.indicados.distinct("cerimonia").length
```
---
## Nível 2 - Explorando Categorias 
2.1 Quantas indicações existem para cada categoria? Agrupe por categoria e ordene da mais frequente para a menos frequente.

2.2 Qual categoria teve mais indicações ao longo da história do Oscar?

2.3 Qual categoria teve menos indicações ao longo da história?

2.4 A partir de que ano a categoria "ACTRESS" deixou de existir? (Dica: procure a última cerimônia com essa categoria)

2.5 Quais categorias existiam na primeira cerimônia (1928) e não existem mais hoje?

2.6 Liste todas as categorias que contêm a palavra "DIRECTING" no nome.


