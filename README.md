## Oscar - Exercícios MongoDB

1.1 Quantos registros existem na coleção de indicados ao Oscar?

R: db.indicados.countDocuments() 
11104

1.2 Quais são as diferentes categorias de premiação que existem no banco de dados? Liste todas as categorias únicas.

R: 122 registros 
db.indicados.distinct("categoria").length
db.indicados.distinct("categoria")

1.3 Qual foi o primeiro ano de cerimônia do Oscar registrado na base?

R: 
