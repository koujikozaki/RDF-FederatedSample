# 統合クエリのSampleおよび動作テスト
統合クエリのSampleの動作確認を行った結果です．

## ジャパンサーチ

https://jpsearch.go.jp/rdf/sparql/easy/



### 動いた例
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?wd WHERE {
   chname:葛飾北斎 owl:sameAs ?wd .
   ?wd rdfs:isDefinedBy <http://www.wikidata.org/> .

   SERVICE <http://dbpedia.org/sparql> {
            ?s ?p ?wd.
        }
}
```

### 動いた例
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?wd WHERE {
   chname:葛飾北斎 owl:sameAs ?wd .
   ?wd rdfs:isDefinedBy <http://www.wikidata.org/> .

   SERVICE <http://ja.dbpedia.org/sparql> {
            ?s ?p ?wd.
        }
}```


### 動かない　→　日本語のURIに不具合あり？
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?dbp WHERE {
   chname:葛飾北斎 owl:sameAs ?dbp .
   ?dbp rdfs:isDefinedBy <http://ja.dbpedia.org/> .

   SERVICE <http://ja.dbpedia.org/sparql> {
           ?dbp ?s ?p .
        }
}```


### 動いた
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?o WHERE {
   SERVICE <http://ja.dbpedia.org/sparql> {
           ?dbp rdfs:label "葛飾北斎"@ja .
        }
   ?s owl:sameAs ?dbp .
   ?dbp rdfs:isDefinedBy <http://ja.dbpedia.org/> .
   ?s ?p ?o. 
}```

### 動かない　→　タイムアウトが発生？
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?o WHERE {

   ?s owl:sameAs ?dbp .
   ?dbp rdfs:isDefinedBy <http://ja.dbpedia.org/> .
   ?s ?p ?o. 

   SERVICE <http://ja.dbpedia.org/sparql> {
           ?dbp rdfs:label "葛飾北斎"@ja .
        }
}```
 

### 動かない →　FILTERがダメ？ 
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?o WHERE {

   SERVICE <http://ja.dbpedia.org/sparql> {
           ?dbp <http://dbpedia.org/ontology/birthDate> ?bdate .
            FILTER(?bdate >= "1750-01-01"^^xsd:date && ?bdate < "1800-01-01"^^xsd:date)  
       }

   ?s owl:sameAs ?dbp .
   ?dbp rdfs:isDefinedBy <http://ja.dbpedia.org/> .
   ?s ?p ?o. 

}```



### 動かない（結果が空）　→　日本語URIがダメ？
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?o WHERE {

   SERVICE <http://ja.dbpedia.org/sparql> {
           ?dbp <http://dbpedia.org/ontology/field> <http://ja.dbpedia.org/resource/浮世絵> .
       }

  ?s owl:sameAs ?dbp .
     ?dbp rdfs:isDefinedBy <http://ja.dbpedia.org/> .
     ?s ?p ?o. 

}```

### 動いた
```PREFIX chname: <https://jpsearch.go.jp/entity/chname/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX dct: <http://purl.org/dc/terms/>

SELECT DISTINCT ?s ?p ?o WHERE {

   SERVICE <http://dbpedia.org/sparql> {
           ?dbp <http://dbpedia.org/ontology/field> <http://dbpedia.org/resource/Ukiyo-e> .
       }

  ?s owl:sameAs ?dbp .
     ?dbp rdfs:isDefinedBy <http://dbpedia.org/> .
     ?s ?p ?o. 

}```