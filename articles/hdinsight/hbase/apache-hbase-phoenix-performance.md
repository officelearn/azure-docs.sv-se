---
title: Phoenix-prestanda i Azure HDInsight
description: Metodtips för att optimera Apache Phoenix-prestanda för Azure HDInsight-kluster
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 7f8f20be81e815414c283f7ec48aa6503e3b60ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552652"
---
# <a name="apache-phoenix-performance-best-practices"></a>Metodtips för prestanda för Apache Phoenix

Den viktigaste aspekten av [Apache Phoenix](https://phoenix.apache.org/) prestanda är att optimera den underliggande [Apache HBase](https://hbase.apache.org/). Phoenix skapar en relationsdatamodell ovanpå HBase som konverterar SQL-frågor till HBase-åtgärder, till exempel genomsökningar. Utformningen av tabellschemat, valet och ordningen på fälten i primärnyckeln och användningen av index påverkar phoenix prestanda.

## <a name="table-schema-design"></a>Design för tabellschema

När du skapar en tabell i Phoenix lagras den tabellen i en HBase-tabell. HBase-tabellen innehåller grupper av kolumner (kolumnfamiljer) som nås tillsammans. En rad i Phoenix-tabellen är en rad i tabellen HBase, där varje rad består av versionsceller som är associerade med en eller flera kolumner. Logiskt sett är en enda HBase-rad en samling nyckel-värde-par, var och en med samma radnyckelvärde. Det vill än, varje nyckelvärdespar har ett rowkey-attribut och värdet för det radnyckelattributet är detsamma för en viss rad.

Schemadesignen för en Phoenix-tabell innehåller primärnyckeldesign, kolumnfamiljdesign, individuell kolumndesign och hur data partitioneras.

### <a name="primary-key-design"></a>Design för primärnyckel

Primärnyckeln som definierats i en tabell i Phoenix avgör hur data lagras i radnyckeln för den underliggande HBase-tabellen. I HBase är det enda sättet att komma åt en viss rad med radnyckeln. Dessutom sorteras data som lagras i en HBase-tabell efter radnyckeln. Phoenix skapar radnyckelvärdet genom att sammanfoga värdena för var och en av kolumnerna på raden, i den ordning de definieras i primärnyckeln.

En tabell för kontakter har till exempel förnamn, efternamn, telefonnummer och adress, alla i samma kolumnfamilj. Du kan definiera en primärnyckel baserat på ett ökande sekvensnummer:

|radnyckel|       adress|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Men om du ofta frågar efter efternamn kanske den här primärnyckeln inte fungerar bra, eftersom varje fråga kräver en fullständig tabellgenomsökning för att läsa värdet för varje efternamn. I stället kan du definiera en primärnyckel i kolumnerna efternamn, förnamn och personnummer. Denna sista kolumn är att disambiguate två invånare på samma adress med samma namn, till exempel en far och son.

|radnyckel|       adress|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Med denna nya primära nyckel radnycklar som genereras av Phoenix skulle vara:

|radnyckel|       adress|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

På den första raden ovan representeras data för radnyckeln som visad:

|radnyckel|       key|   värde|
|------|--------------------|---|
|  Dole-John-111|adress |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111|

Den här radnyckeln lagrar nu en dubblettkopia av data. Tänk på storleken och antalet kolumner som du inkluderar i primärnyckeln, eftersom det här värdet ingår i varje cell i den underliggande HBase-tabellen.

Om primärnyckeln har värden som ökar monotoniskt bör du också skapa tabellen med *saltskopor* för att undvika att skapa skrivpunkter - se [Partitionsdata](#partition-data).

### <a name="column-family-design"></a>Kolumn familj design

Om vissa kolumner används oftare än andra bör du skapa flera kolumnfamiljer för att skilja de kolumner som används ofta från kolumner som används sällan.

Om vissa kolumner tenderar att nås tillsammans placerar du även kolumnerna i samma kolumnfamilj.

### <a name="column-design"></a>Kolumndesign

* Behåll VARCHAR-kolumner under ca 1 MB på grund av I/O-kostnaderna för stora kolumner. Vid bearbetning av frågor materialiserar HBase cellerna i sin helhet innan de skickas över till klienten, och klienten tar emot dem i sin helhet innan de överlämnas till programkoden.
* Lagra kolumnvärden med ett kompakt format som protobuf, Avro, msgpack eller BSON. JSON rekommenderas inte, eftersom det är större.
* Överväg att komprimera data före lagring för att minska svarstiden och I/O-kostnaderna.

### <a name="partition-data"></a>Partitionera data

Phoenix kan du styra antalet regioner där dina data distribueras, vilket avsevärt kan öka läs-/ skrivprestanda. När du skapar en Phoenix-tabell kan du antingen salta eller dela upp dina data i förakan.

Om du vill salta en tabell under skapandet anger du antalet salthinkar:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Den här saltningen delar tabellen längs värdena för primärnycklar och väljer värdena automatiskt. 

Om du vill styra var tabelldelningen sker kan du dela upp tabellen i föraningen genom att ange de intervallvärden längs vilka delningen sker. Så här skapar du till exempel en tabell som delas mellan tre regioner:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Indexdesign

Ett Phoenix-index är en HBase-tabell som lagrar en kopia av vissa eller alla data från den indexerade tabellen. Ett index förbättrar prestanda för specifika typer av frågor.

När du har definierat flera index och sedan frågar efter en tabell väljer Phoenix automatiskt det bästa indexet för frågan. Det primära indexet skapas automatiskt baserat på de primära nycklar du väljer.

För förväntade frågor kan du också skapa sekundära index genom att ange deras kolumner.

När du utformar dina index:

* Skapa bara de index du behöver.
* Begränsa antalet index i ofta uppdaterade tabeller. Uppdateringar av en tabell översätter till skrivningar till både huvudtabellen och indextabellerna.

## <a name="create-secondary-indexes"></a>Skapa sekundära index

Sekundära index kan förbättra läsprestanda genom att förvandla vad som skulle vara en fullständig tabellsökning till en punktsökning, på bekostnad av lagringsutrymme och skrivhastighet. Sekundära index kan läggas till eller tas bort när tabellen har skapats och kräver inte ändringar i befintliga frågor – frågor körs bara snabbare. Beroende på dina behov kan du överväga att skapa täckta index, funktionella index eller båda.

### <a name="use-covered-indexes"></a>Använd täckta index

Täckta index är index som innehåller data från raden utöver de värden som indexeras. När du har hittat önskad indexpost behöver du inte komma åt den primära tabellen.

I exempelkontakttabellen kan du till exempel skapa ett sekundärt index på bara kolumnen socialSecurityNum. Det här sekundära indexet skulle påskynda frågor som filtrerar efter socialaSecurityNum-värden, men om du hämtar andra fältvärden krävs ytterligare en läsning mot huvudtabellen.

|radnyckel|       adress|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Om du vanligtvis vill slå upp förnamn och efternamn med tanke på socialSecurityNum kan du skapa ett täckt index som innehåller förnamn och efternamn som faktiska data i indextabellen:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Med det här indexet kan följande fråga hämta alla data bara genom att läsa från tabellen som innehåller det sekundära indexet:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Använda funktionella index

Med funktionella index kan du skapa ett index på ett godtyckligt uttryck som du förväntar dig ska användas i frågor. När du har ett funktionellt index på plats och en fråga använder det uttrycket kan indexet användas för att hämta resultaten i stället för datatabellen.

Du kan till exempel skapa ett index så att du kan göra skiftlägesokänsliga sökningar på en persons kombinerade för- och efternamn:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Frågedesign

De viktigaste övervägandena i frågedesign är:

* Förstå frågeplanen och verifiera dess förväntade beteende.
* Gå med effektivt.

### <a name="understand-the-query-plan"></a>Förstå frågeplanen

I [SQLLine](http://sqlline.sourceforge.net/)använder du EXPLAIN följt av SQL-frågan för att visa planen för åtgärder som Phoenix ska utföra. Kontrollera att planen:

* Använder primärnyckeln när det är lämpligt.
* Använder lämpliga sekundära index i stället för datatabellen.
* Använder RANGE SCAN eller SKIP SCAN när det är möjligt, i stället för TABELLSKANNING.

#### <a name="plan-examples"></a>Planera exempel

Anta till exempel att du har en tabell som heter FLYG som lagrar information om försenade flyg.

Så här väljer du alla `19805`flygningar med ett flygbolag , där airlineid är ett fält som inte finns i primärnyckeln eller i något index:

    select * from "FLIGHTS" where airlineid = '19805';

Kör förklaringskommandot på följande sätt:

    explain select * from "FLIGHTS" where airlineid = '19805';

Frågeplanen ser ut så här:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

I den här planen noterar du frasen Full SCAN OVER FLIGHTS. Den här frasen anger att körningen gör en TABELLSKANNING över alla rader i tabellen, i stället för att använda alternativet effektivare INTERVALLSKANNING eller SKIP SCAN.

Säg nu att du vill fråga efter flyg den 2 januari `AA` 2014 för det flygbolag där dess flygnummer var större än 1. Anta att kolumnerna år, månad, månad, månad, transportör och flygnummer finns i exempeltabellen och alla är en del av den sammansatta primärnyckeln. Frågan skulle se ut så här:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Låt oss undersöka planen för den här frågan med:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Den resulterande planen är:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Värdena inom hakparenteser är olika värden för primärnycklarna. I det här fallet fastställs intervallvärdena med år 2014, månad 1 och dag månad 2, men`*`tillåter värden för flightnum som börjar med 2 och uppåt ( ). Den här frågeplanen bekräftar att primärnyckeln används som förväntat.

Skapa sedan ett index i `carrier2_idx` tabellen FLYG med namnet som endast finns i fältet transportör. Detta index omfattar även flightdate, tailnum, origin och flightnum som täckta kolumner vars data också lagras i indexet.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Säg att du vill få med transportören med flightdate och tailnum, som i följande fråga:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Du bör se detta index som används:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

En fullständig lista över de objekt som kan visas i förklaringsplanresultat finns i avsnittet Förklara planer i [Apache Phoenix Tuning Guide](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Gå med effektivt

I allmänhet vill du undvika kopplingar om inte den ena sidan är liten, särskilt på vanliga frågor.

Om det behövs kan du `/*+ USE_SORT_MERGE_JOIN */` göra stora kopplingar med tipset, men en stor koppling är en dyr operation över ett stort antal rader. Om den totala storleken på alla högertabeller skulle överskrida `/*+ NO_STAR_JOIN */` det tillgängliga minnet använder du tipset.

## <a name="scenarios"></a>Scenarier

Följande riktlinjer beskriver några vanliga mönster.

### <a name="read-heavy-workloads"></a>Lästunga arbetsbelastningar

För lästunga användningsfall kontrollerar du att du använder index. Dessutom, för att spara lästid omkostnader, överväga att skapa täckta index.

### <a name="write-heavy-workloads"></a>Skrivtunga arbetsbelastningar

För skrivtunga arbetsbelastningar där primärnyckeln ökar monotoniskt, skapa salthinkar för att undvika att skriva hotspots, på bekostnad av det totala läsflödet på grund av de ytterligare genomsökningar som behövs. När du använder UPSERT för att skriva ett stort antal poster stänger du också av autoCommit och batchar upp posterna.

### <a name="bulk-deletes"></a>Massborttagningar

När du tar bort en stor datauppsättning aktiverar du autoCommit innan du utfärdar DELETE-frågan, så att klienten inte behöver komma ihåg radtangenterna för alla borttagna rader. AutoCommit hindrar klienten från att buffra raderna som påverkas av DELETE, så att Phoenix kan ta bort dem direkt på regionservrarna utan att du erar dem tillbaka till klienten.

### <a name="immutable-and-append-only"></a>Oföränderliga och bifogade

Om ditt scenario gynnar skrivhastighet över dataintegritet kan du inaktivera skriv-framåt-loggen när du skapar tabeller:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Mer information om detta och andra alternativ finns i [Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Nästa steg

* [Guiden Apache Phoenix-justering](https://phoenix.apache.org/tuning_guide.html)
* [Sekundära index](https://phoenix.apache.org/secondary_indexing.html)
