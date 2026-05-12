## Oscar - Exercícios MongoDB

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
