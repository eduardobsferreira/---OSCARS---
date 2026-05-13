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

R: 
```
{
    $group: {
      _id: "$categoria",
      total_indicacoes: { $sum: 1 }
    }
  },
  {
    $sort: { total_indicacoes: -1 }
  }
])

#Mais frequente
db.indicados.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
])

#Menos frequente
db.indicados.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: 1 } },
  { $limit: 1 }
])

```

2.2 Qual categoria teve mais indicações ao longo da história do Oscar?

R:  _id: 'DIRECTING',
  total: 479 
```
db.indicados.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
])

```
2.3 Qual categoria teve menos indicações ao longo da história?

R:_id: 'AWARD OF COMMENDATION',
  total: 1
```
db.indicados.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: 1 } },
  { $limit: 1 }
])
```
2.4 A partir de que ano a categoria "ACTRESS" deixou de existir? (Dica: procure a última cerimônia com essa categoria)
R: 1976
```
db.indicados.aggregate([
  { $match: { categoria: "ACTRESS" } },
  { $sort: { ano_cerimonia: -1 } },
  { $limit: 1 },
  { $project: { ano_cerimonia: 1, cerimonia: 1, _id: 0 } }
])
```

2.5 Quais categorias existiam na primeira cerimônia (1928) e não existem mais hoje?
R:  desaparecidas: [
    {
      _id: 'ART DIRECTION'
    },
    {
      _id: 'ACTRESS'
    },
    {
      _id: 'ENGINEERING EFFECTS'
    },
    {
      _id: 'OUTSTANDING PICTURE'
    },
    {
      _id: 'UNIQUE AND ARTISTIC PICTURE'
    },
    {
      _id: 'WRITING (Adaptation)'
    },
    {
      _id: 'WRITING (Title Writing)'
    },
    {
      _id: 'SPECIAL AWARD'
    },
    {
      _id: 'ACTOR'
    },
    {
      _id: 'WRITING (Original Story)'
    },
    {
      _id: 'DIRECTING (Dramatic Picture)'
    },
    {
      _id: 'DIRECTING (Comedy Picture)'
    }
  ]
}
```
db.indicados.aggregate([ {
    $facet: {
      categorias_1928: [
        { $match: { ano_cerimonia: 1928 } },
        { $group: { _id: "$categoria" } }
      ],
      categorias_recentes: [
        { $match: { ano_cerimonia: { $gte: 2020 } } },
        { $group: { _id: "$categoria" } }
      ]
    }
  }, {
    $project: {
      desaparecidas: {
        $filter: {
          input: "$categorias_1928",
          as: "cat",
          cond: {
            $not: {
              $in: ["$$cat._id", "$categorias_recentes._id"]
            }
          }
        }
      }
    }
  }
])
```

2.6 Liste todas as categorias que contêm a palavra "DIRECTING" no nome.

R: 'DIRECTING',
  'DIRECTING (Comedy Picture)',
  'DIRECTING (Dramatic Picture)'
```
db.indicados.distinct(
  "categoria",
  { categoria: /DIRECTING/i }
)
```
  
## Nível 3 


