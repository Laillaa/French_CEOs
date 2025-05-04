## Explore Wikidata

In this notebook, we refine and document the main requests available on the page [Exploration of Wikidata](../documentation/wikidata/Wikidata-exploration.md) 


When you prepare the queries, you can execute them on the Wikidata SPARQL endpoint, and then document and execute them in this notebook.

Countries: France, Sweden & New Zealand

### Inspect CEOs 

#### Modern ceos: born from 1800 onward
```sparql
SELECT (count(*) as ?number)
WHERE {
    {?item wdt:P106 wd:Q484876}  # Occupation: CEO
    UNION
    {?item wdt:P101 wd:Q8187769}     # economic activity 
    
    ?item wdt:P31 wd:Q5; # Any instance of a human.
            wdt:P569 ?birthDate.
    

    BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
    FILTER(xsd:integer(?year) > 1800 )
}
```
2903 CEOs born from 1800 onward (All nationalities)

#### Gives us the list of:

The French CEOs
```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

   ## Count and inspect occupations and fields of work
   SELECT ?item ##(COUNT(*) as ?eff)
    WHERE {
        ?item wdt:P31 wd:Q5;  # Any instance of a human.

            wdt:P106 wd:Q484876; # Occupation: CEO
            wdt:P27 wd:Q142 # country of citizenship: France

    }  
```

The Swedish CEOs
```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

   ## Count and inspect occupations and fields of work
   SELECT ?item ##(COUNT(*) as ?eff)
    WHERE {
        ?item wdt:P31 wd:Q5;  # Any instance of a human.

            wdt:P106 wd:Q484876; # Occupation: CEO
            wdt:P27 wd:Q34 # country of citizenship: Sweden

    }  
```

The New Zealand CEOs
```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

   ## Count and inspect occupations and fields of work
   SELECT ?item ##(COUNT(*) as ?eff)
    WHERE {
        ?item wdt:P31 wd:Q5;  # Any instance of a human.

            wdt:P106 wd:Q484876; # Occupation: CEO
            wdt:P27 wd:Q664 # country of citizenship: New Zealand

    } 
```

#### Gives us the n. of ceos.

The French CEOs
```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

   ## Count and inspect occupations and fields of work
   SELECT (count(*) as ?number)
    WHERE {
        ?item wdt:P31 wd:Q5;  # Any instance of a human.

            wdt:P106 wd:Q484876; # Occupation: CEO
            wdt:P27 wd:Q142 # country of citizenship: France

    }  
```

The Swedish CEOs
```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

   ## Count and inspect occupations and fields of work
   #SELECT ?item ##(COUNT(*) as ?eff)
   SELECT (count(*) as ?number)
    WHERE {
        ?item wdt:P31 wd:Q5;  # Any instance of a human.

            wdt:P106 wd:Q484876; # Occupation: CEO
            wdt:P27 wd:Q34 # country of citizenship: Sweden

    }  
```

The New Zealand CEOs
```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

   ## Count and inspect occupations and fields of work
   #SELECT ?item ##(COUNT(*) as ?eff)
   SELECT (count(*) as ?number)
    WHERE {
        ?item wdt:P31 wd:Q5;  # Any instance of a human.

            wdt:P106 wd:Q484876; # Occupation: CEO
            wdt:P27 wd:Q664 # country of citizenship: New Zealand

    }  
```

### Count how many properties are available for the considered population

Execute this query on the Wikidata sparql-endpoint and save the result to a CSV document that you will store in your project: [population properties list](../Data/Query.csv)


Open your CSV file with a spreadsheet editor:
* Inspect the content of the results and look for relevant properties with regard to your research questions
* Observe all the links to other semantic web repositories, probably the sources of this information.
* You can transform this file to your preferred spreadsheet editor format (Calc, Excel, etc.) and take notes row per row in the spreadsheet.


```sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

SELECT ?p ?propLabel ?eff
WHERE {
{
SELECT ?p  (count(*) as ?eff)
WHERE {
    ?item wdt:P106 wd:Q484876.  # Occupation: CEO
    
    {?item wdt:P27 wd:Q142} # country of citizenship: France
    UNION
    {?item wdt:P27 wd:Q34} # country of citizenship: Sweden
    UNION
    {?item wdt:P27 wd:Q664} # country of citizenship: New Zealand


    ?item wdt:P31 wd:Q5; # Any instance of a human.
            wdt:P569 ?birthDate.
    ?item  ?p ?o.

    BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
    FILTER(xsd:integer(?year) > 1800)
    #FILTER(?country = "France") # country of citizenship: France


}
GROUP BY ?p 

    }

# get the original property (in the the statement construct)     
?prop wikibase:directClaim ?p .

SERVICE wikibase:label { bd:serviceParam wikibase:language "en". } 
SERVICE wikibase:label { bd:serviceParam wikibase:language "fr". } 


}  
ORDER BY DESC(?eff)
```
