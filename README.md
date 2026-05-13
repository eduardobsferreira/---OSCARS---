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
  
## Nível 3 - : Atores e Atrizes Famosos

## Natalie Portman

3.1 Quantas vezes Natalie Portman foi indicada ao Oscar?

R: 3
```
db.indicados.countDocuments({nome_do_indicado: "Natalie Portman"})
```

3.2 Quantos Oscars Natalie Portman ganhou?
R: 1
```
db.indicados.countDocuments({nome_do_indicado: "Natalie Portman", vencedor:"true"})
```
3.3 Em quais anos e por quais filmes Natalie Portman foi indicada?
R: {
  ano_cerimonia: 2005,
  nome_do_filme: 'Closer'
}
{
  ano_cerimonia: 2011,
  nome_do_filme: 'Black Swan'
}
{
  ano_cerimonia: 2011,
  nome_do_filme: 'Black Swan'
}
```
db.indicados.find(
  { nome_do_indicado: "Natalie Portman" },
  { ano_cerimonia: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })
```



3.4 Liste todas as indicações de Natalie Portman mostrando: ano, categoria, filme e se venceu.

R: 
```
db.indicados.find(
  { nome_do_indicado: "Natalie Portman" },
  { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })
```
---
## Viola Davis
3.5 Quantas vezes Viola Davis foi indicada ao Oscar?
R: 4 
```
db.indicados.countDocuments({nome_do_indicado: "Viola Davis"})
```
3.6 Quantos Oscars Viola Davis ganhou?
R: 1
```
db.indicados.countDocuments({nome_do_indicado: "Viola Davis", vencedor: "true"})
```
3.7 Por quais filmes Viola Davis foi indicada?
R:
```
db.indicados.find(
  { nome_do_indicado: "Viola Davis" },
  { ano_cerimonia: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })
```
---
## Amy Adams
3.8 Amy Adams já ganhou algum Oscar?
R: Não 
```
db.indicados.countDocuments({nome_do_indicado: "Amy Adams", vencedor: "true"})
```
3.9 Quantas vezes Amy Adams foi indicada sem ganhar?
R: 6 
```
db.indicados.find(
  { nome_do_indicado: "Amy Adams" },
  { ano_cerimonia: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })
```
---
Denzel Washington
3.10 Denzel Washington já ganhou algum Oscar?
R: 2
```
db.indicados.countDocuments({nome_do_indicado: "Denzel Washington", vencedor: "true"})
```
3.11 Quantas vezes Denzel Washington foi indicado ao Oscar?
R: 9 
```
db.indicados.countDocuments({nome_do_indicado: "Denzel Washington"})
```
3.12 Liste todos os Oscars que Denzel Washington ganhou (ano, categoria, filme).
R:
```
db.indicados.find(
  { nome_do_indicado: "Denzel Washington" },
  { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })
```
---
## Nível 4: Vencedores Históricos
4.1 Quem ganhou o primeiro Oscar para Melhor Atriz (ACTRESS)? Em que ano e por qual filme?

R: Janet Gaynor
```
db.indicados.findOne(
  { categoria: "ACTRESS", vencedor: "true" },
  { ano_cerimonia: 1, nome_do_indicado: 1, nome_do_filme: 1, _id: 0 },
  { sort: { ano_cerimonia: 1 } }
)
```
4.2 Quem ganhou o primeiro Oscar para Melhor Ator (ACTOR)? Em que ano e por qual filme?
R: Emil Jannings
```
db.indicados.findOne(
  { categoria: "ACTOR", vencedor: "true" },
  { ano_cerimonia: 1, nome_do_indicado: 1, nome_do_filme: 1, _id: 0 },
  { sort: { ano_cerimonia: 1 } }
)
```

4.3 Quantos vencedores existem ao todo na base de dados?
R: 2507
```
db.indicados.countDocuments({ vencedor: "true" })
```

4.4 Liste todos os filmes que ganharam o Oscar de Melhor Filme (categoria "OUTSTANDING PICTURE" ou "BEST PICTURE").
R: 
```
db.indicados.find(
  {
    categoria: { $in: ["OUTSTANDING PICTURE", "BEST PICTURE"] },
    vencedor: "true"
  },
  { ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, _id: 0 }
).sort({ ano_cerimonia: 1 })
```

4.5 Quantos filmes diferentes já ganharam o Oscar?
R:1354
```
db.indicados.aggregate([
  { $match: { vencedor: "true" } },
  { $group: { _id: "$nome_do_filme" } },
  { $count: "total_filmes_vencedores" }
])
```
---
## Nível 5 - Análise de Indicações

5.1 Quais atores/atrizes foram indicados mais de uma vez? Liste o nome e o número de indicações.

R: 
```
db.indicados.aggregate([
  { $group: { _id: "$nome_do_indicado", total: { $sum: 1 } } },
  { $match: { total: { $gt: 1 } } },
  { $sort: { total: -1 } }
])
```

5.2 Qual ator ou atriz tem o maior número de indicações na história do Oscar?
R: 'Metro-Goldwyn-Mayer
```
db.indicados.aggregate([
  { $group: { _id: "$nome_do_indicado", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
])
```

5.3 Quais atores foram indicados mais de 3 vezes, mas nunca ganharam?
R:

5.4 Encontre todos os artistas que foram indicados em categorias diferentes (ex: ator e diretor).
R:

5.5 Quantos indicados têm exatamente 1 indicação na história?
R:

5.6 Qual o maior números de indicados em um único ano? Essa é uma pergunta franca.
R:

---
