## Explore Wikidata
!! do the csv part!!

In this notebook, we refine and document the main requests available on the page [Exploration of Wikidata](../documentation/wikidata/Wikidata-exploration.md) 


When you prepare the queries, you can execute them on the Wikidata SPARQL endpoint, and then document and execute them in this notebook.
### Explore occupations and fields of work

```sparql
### List 'n' more frequent occupations

PREFIX wd: <http://www.wikidata.org/entity/>


SELECT ?occupation ?occupationLabel ?n
WHERE {

    {
    SELECT ?occupation (COUNT(*) as ?n)
    WHERE {
        ?item wdt:P106 ?occupation.
        }
    GROUP BY ?occupation 
    ORDER BY DESC(?n)

    #OFFSET 20
    LIMIT 20
    }

    SERVICE wikibase:label { bd:serviceParam wikibase:language "en" }
    
    }
    ORDER BY DESC(?n)


```
#### Outcome: 

| occupation | occupationLabel | n |
| :---         |     :---:      |          ---: |
| wd:Q1650915 	| researcher |	1998171 |
| wd:Q82955	| politician	| 858418 |
| wd:Q36180	| writer	| 380753 |
| wd:Q937857	| association football player | 371772 |
| wd:Q33999	| actor | 349653 |
| wd:Q1622272	| university teacher | 274866 |
| wd:Q1028181	| painter | 213368 |
| wd:Q1930187	| journalist | 174313 |
| wd:Q3665646	| basketball player	| 172808 |
| wd:Q49757	| poet | 121476 |
| wd:Q177220	| singer	| 117271|
| wd:Q201788	| historian	| 115095 |
| wd:Q36834	| composer	| 110903 |
| wd:Q47064	| military personnel	| 103301 |
| wd:Q39631	| physician	| 100614 |
| wd:Q40348	| lawyer	| 99836 |
| wd:Q2526255	| film director	| 97189 |
| wd:Q639669	| musician	| 93226 |
| wd:Q37226	| teacher	| 85767 |
| wd:Q42973	| architect	| 85687|

```sparql
### List more frequent occupations

PREFIX wd: <http://www.wikidata.org/entity/>

SELECT ?field ?fieldLabel ?n
WHERE {

    {
    SELECT ?field (COUNT(*) as ?n)
    WHERE {
        ?item wdt:P101 ?field.
        }
    GROUP BY ?field 
    ORDER BY DESC(?n)

    #OFFSET 20
    LIMIT 20
    }

    SERVICE wikibase:label { bd:serviceParam wikibase:language "en" }
    
    }
    ORDER BY DESC(?n)


```
#### Outcome: 

| field | fieldLabel | n |
| :---         |     :---:      |          ---: |
| wd:Q184485	| performing arts| 28637         |
| wd:Q309	   | history        |	20415       |
| wd:Q11190	   | medicine | 16953 |
| wd:Q8242     |	literature | 13732 |
| wd:Q482      |	poetry | 13031 |
| wd:Q11030    |	journalism | 12703 
| wd:Q11030    |	journalism | 12703 |
| wd:Q11629    |	art of painting |11735 |
| wd:Q638      | music | 11067 |
| wd:Q113209507 |	creative and professional writing |8789 |
| wd:Q36649 |	visual arts | 8776 | 
| wd:Q395 | mathematics | 8229 |
| wd:Q5891 | philosophy | 8038 |
| wd:Q7748 | law | 7962 |
| wd:Q7163 | politics | 7710 |
| wd:Q50637 | art history | 7590 |
| wd:Q8134 | economics | 7017 |
| wd:Q115160290 | literary activity | 6584 |
| wd:Q12271 | architecture | 6548 |
| wd:Q156035 | opinion journalism | 6522 |
| wd:Q222749 | acting | 6376 |

### Inspect CEOs and related occupations

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
    LIMIT 200
```

```sparql
### Modern ceos : born from 1800 onward
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
2903 CEOs born from 1800 onward

```sparql
### Modern ceos
SELECT (count(*) as ?number)
WHERE {
    {?item wdt:P106 wd:Q484876}  # # Occupation: CEO
    UNION
    {?item wdt:P101 wd:Q8187769}     # economic activity
    ?item wdt:P31 wd:Q5; # Any instance of a human.
            wdt:P569 ?birthDate.
    

    BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
    FILTER(xsd:integer(?year) > 1800  && xsd:integer(?year) < 1951 )
}
```
791 CEOs born between 1800 and  1951

### Count how many properties are available for the considered population

Execute this query on the Wikidata sparql-endpoint and save the result to a CSV document that you will store in your project: [population properties list](../Wikidata/properties_20250309.csv)


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
    {?item wdt:P106 wd:Q484876}  # Occupation: CEO
    UNION
    {?item wdt:P27 wd:Q142}  # country of citizenship: France
    UNION
    {?item wdt:P101 wd:Q8187769}     # economic activity   
    ?item wdt:P31 wd:Q5; # Any instance of a human.
            wdt:P569 ?birthDate.
    ?item  ?p ?o.

    BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
    ### Experiment with different time filters if too many values
    FILTER(xsd:integer(?year) > 1750  && xsd:integer(?year) < 1951)
    # FILTER(xsd:integer(?year) > 1850  && xsd:integer(?year) < 1951)

}
GROUP BY ?p 

    }

# get the original property (in the the statement construct)     
?prop wikibase:directClaim ?p .

SERVICE wikibase:label { bd:serviceParam wikibase:language "en". } 


}  
ORDER BY DESC(?eff)
```
