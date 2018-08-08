---
title: Phoenix prestanda i Azure HDInsight
description: Bästa praxis för att optimera prestanda för Phoenix.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: db00dcad8f3dffbb958158bef9fdb75423eba2f7
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592279"
---
# <a name="phoenix-performance-best-practices"></a>Metodtips för prestanda för Phoenix

Den viktigaste delen av prestanda för Phoenix är att optimera den underliggande HBase. Phoenix skapar en relationsdatamodell ovanpå HBase som konverterar SQL-frågor till HBase-åtgärder, till exempel genomsökningar. Utformningen av din tabellschemat, markeringen och ordning på fälten i den primära nyckeln och din användning av alla index påverka prestanda för Phoenix.

## <a name="table-schema-design"></a>Tabelldesign schema

När du skapar en tabell i Phoenix lagras tabellen i en HBase-tabell. HBase-tabellen innehåller grupper med kolumner (kolumnserier) som används tillsammans. En rad i tabellen Phoenix är en rad i HBase-tabellen, där varje rad består av en ny version celler som är associerade med en eller flera kolumner. Logiskt, är en enskild rad HBase en samling nyckel / värde-par, som var och en med samma rowkey värde. Det vill säga varje nyckel / värde-par har ett rowkey-attribut och värdet för attributet rowkey är densamma för en viss rad.

Schemat utformningen av en Phoenix-tabell som innehåller primärt nyckeln design, kolumnen family design, enskild kolumn design och hur data är partitionerad.

### <a name="primary-key-design"></a>Primär nyckel design

Den primära nyckeln som definierats i en tabell i Phoenix avgör hur data lagras i rowkey av den underliggande HBase-tabellen. I HBase är det enda sättet att komma åt en viss rad med rowkey. Dessutom kan är data som lagras i ett HBase-tabellen sorterad efter rowkey. Phoenix bygger rowkey värdet genom att sammanfoga värden för var och en av kolumnerna i raden i den ordning som de definieras i den primära nyckeln.

Till exempel har en tabell för kontakter i förnamn, senaste namn, telefonnummer och adress, allt i samma kolumnserie. Du kan definiera en primärnyckel som baseras på ett ökande sekvensnummer:

|rowkey|       Adress|   telefon| Förnamn| Efternamn|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Men om du frågar ofta efter efternamn kan den här primära nyckeln inte utföra, eftersom varje fråga kräver en fullständig tabellsökning att läsa värdet för varje efternamn. I stället kan du definiera en primärnyckel på lastName, firstName och personnummer kolumner. Det här sista kolumnen är att undvika två invånare med samma adress med samma namn, till exempel en far och son.

|rowkey|       Adress|   telefon| Förnamn| Efternamn| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Med den här nya primärnyckel raden skulle nycklar som genereras av Phoenix bli:

|rowkey|       Adress|   telefon| Förnamn| Efternamn| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole John 111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

I den första raden ovan visas data för rowkey som visas:

|rowkey|       key|   värde| 
|------|--------------------|---|
|  Dole John 111|Adress |1111 San Gabriel Dr.|  
|  Dole John 111|telefon |1-425-000-0002|  
|  Dole John 111|Förnamn |John|  
|  Dole John 111|Efternamn |Dole|  
|  Dole John 111|socialSecurityNum |111| 

Den här rowkey nu lagrar en kopia av data. Beakta storlek och antalet kolumner som du lägger till i den primära nyckeln, eftersom det här värdet som ingår i varje cell i den underliggande HBase-tabellen.

Även om den primära nyckeln har värden som monotont ökar, bör du skapa tabellen med *salt buckets* till att undvika att skapa write-anslutningar – Se [partitionera data](#partition-data).

### <a name="column-family-design"></a>Kolumnen family design

Om vissa kolumner som används oftare än andra, bör du skapa flera kolumnserier för att avgränsa kolumnerna som används ofta från data som sällan används kolumner.

Även om vissa kolumner brukar användas tillsammans, placera dessa kolumner i samma kolumnserie.

### <a name="column-design"></a>Kolumnen design

* Behåll VARCHAR-kolumner under ca 1 MB på grund av i/o-kostnader med stora kolumner. Vid bearbetning av frågor, HBase materialiserar celler med fullständigt innan de skickas över till klienten, och klienten tar emot dem med fullständigt innan du skickar dem till programkoden.
* Store kolumnvärdena med hjälp av ett kompakt format, till exempel protobuf, Avro, msgpack eller BSON. JSON rekommenderas inte eftersom den är större.
* Överväg att komprimera data före lagring för att klippa ut svarstid och i/o-kostnader.

### <a name="partition-data"></a>Partitionera data

Phoenix kan du styra hur många regioner där dina data distribueras, vilket ökar prestanda för Läs/Skriv. När du skapar en Phoenix-tabell kan du antingen salt eller förväg dela dina data.

Om du vill salt en tabell när du skapar, anger du hur många salt buckets:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Den här saltning delar upp tabellen med värdena för primärnycklar, välja värden automatiskt. 

För att styra där tabellen delningar inträffa, kan du förväg dela upp tabellen genom att tillhandahålla intervallvärden längs vilka uppdelningen av inträffar. Till exempel delar om du vill skapa en tabell längs tre regioner:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Indexdesign

Ett Phoenix-index är en HBase-tabell som lagrar en kopia av vissa eller alla data från tabellen indexerad. Ett index förbättrar prestandan för vissa typer av frågor.

När du har flera index som definierats och fråga en tabell, väljer Phoenix automatiskt det bästa indexet för frågan. Primärt index skapas automatiskt baserat på de primära nycklarna som du väljer.

Du kan också skapa sekundärindex genom att ange sina kolumner för förväntade frågor.

När du utformar ditt index:

* Endast skapa index som du behöver.
* Begränsa antalet index för tabeller som uppdateras ofta. Uppdateringar till en tabell omvandla till skrivningar till både huvudtabellen och indextabellerna.

## <a name="create-secondary-indexes"></a>Skapa sekundärindex

Sekundära index kan förbättra läsprestanda genom att stänga det skulle vara en fullständig tabellsökning till en punkt-sökning, på bekostnad av lagringsutrymme och skrivhastighet. Sekundära index kan läggas till eller tas bort när tabellen har skapats och inte kräver ändringar i befintliga frågor – frågor köras bara snabbare. Överväg att skapa omfattas index eller den funktionella index beroende på dina behov.

### <a name="use-covered-indexes"></a>Använda skyddad index

Skyddad index är index som innehåller data från rad förutom de värden som indexeras. När du har hittat önskad indexposten, finns det inget behov att få åtkomst till den primära tabellen.

Till exempel i det här exemplet kontaktar du tabellen som du kan skapa ett sekundärt index på bara kolumnen socialSecurityNum. Den här sekundära index skulle snabba upp frågorna som filtrera efter socialSecurityNum värden, men hämtning av andra fältvärden kräver en annan läsa mot huvudtabell.

|rowkey|       Adress|   telefon| Förnamn| Efternamn| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole John 111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Om du vill förmodligen att leta upp det förnamn och Efternamn baserat på socialSecurityNum, kan du skapa en skyddad index som innehåller förnamn och efternamn som faktiska data i indextabellen:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Detta omfattas index kan följande fråga för att hämta alla data genom att läsa från den tabell som innehåller det sekundära indexet:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Använda funktionell index

Funktionella index kan du skapa ett index på ett valfritt uttryck som du förväntar dig som ska användas i frågor. När du har ett fungerande index på plats och en fråga använder uttrycket, kan indexet användas till att hämta resultaten i stället för tabellen.

Du kan till exempel skapa ett index för att kunna göra skiftlägesoberoende sökningar på det kombinerade förnamn och efternamn för en person:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Frågans design

De huvudsakliga övervägandena i fråga design är:

* Förstå frågeplanen och verifiera dess förväntat beteende.
* Ansluta till ett effektivt sätt.

### <a name="understand-the-query-plan"></a>Förstå frågeplanen

I [SQLLine](http://sqlline.sourceforge.net/), använda FÖRKLARA följt av SQL-frågan för att visa planen för åtgärder som utförs med Phoenix. Kontrollera att planen:

* Använder den primära nyckeln när det är lämpligt.
* Använder lämpliga sekundära index i stället för tabellen.
* Använder intervall SKANNA eller hoppa över SÖKNING när det är möjligt i stället tabellen SKANNA.

#### <a name="plan-examples"></a>Planera exempel

Anta att du har en tabell med namnet FLYG som lagrar fördröjning flyginformation exempelvis.

Att välja alla flygningar med en airlineid av `19805`, där airlineid är ett fält som är inte i den primära nyckeln eller i ett index:

    select * from "FLIGHTS" where airlineid = '19805';

Kör kommandot förklara enligt följande:

    explain select * from "FLIGHTS" where airlineid = '19805';

Frågeplanen ser ut så här:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Observera frasen fullständig GENOMSÖKNING över FLYG i den här planen. Den här frasen anger körningen utför en tabell som SKANNA via alla rader i tabellen, i stället alternativet för effektivare intervall SKANNA eller hoppa över SÖKNING.

Anta nu att du vill fråga efter flyg 2 januari 2014 för transportföretaget `AA` där dess flightnum var större än 1. Anta att kolumner som år, månad, dag i månaden, operatör och flightnum finns i tabellen exempel och ingår i den sammansatta primära nyckeln. Frågan skulle se ut så här:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Låt oss nu undersöka planen för den här frågan med:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

I planen är:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Värden inom hakparenteser är en uppsättning värden för de primära nycklarna. I det här fallet värdena för identitetsområde korrigeras med år 2014 och månad 1 dag i månaden 2, men Tillåt värden för flightnum startar med 2 och på (`*`). Den här frågeplan bekräftar att den primära nyckeln används som förväntat.

Skapa sedan ett index i tabellen FLYG med namnet `carrier2_idx` som finns på fältet operatör. Det här indexet innehåller också flightdate, tailnum, ursprung och flightnum som omfattas kolumner vars data lagras också i indexet.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Anta att du vill hämta operatör tillsammans med flightdate och tailnum, som i följande fråga:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Du bör se detta index som används:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

En fullständig lista över de objekt som kan visas i förklara plan resultat, finns i avsnittet förklarar planer i den [Apache Phoenix-Justeringsguide](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Ansluta till ett effektivt sätt

I allmänhet bör du undvika kopplingar, såvida inte en sida är liten, särskilt på vanliga frågor.

Om nödvändigt, kan du göra stora kopplingar med den `/*+ USE_SORT_MERGE_JOIN */` tipset, men en stor koppling är en kostsam åtgärd över enorma antalet rader. Om den sammanlagda storleken för alla tabeller för right hand sida skulle överskrida det tillgängliga minnet, använder du den `/*+ NO_STAR_JOIN */` tipset.

## <a name="scenarios"></a>Scenarier

Följande riktlinjer beskriver några vanliga mönster.

### <a name="read-heavy-workloads"></a>Läs tunga arbetsbelastningar

Läs omfattande användningsfall, se till att du använder index. Dessutom för att spara Läs-time omkostnader, Överväg att skapa omfattas index.

### <a name="write-heavy-workloads"></a>Skrivintensiv arbetsbelastningar

Skapa salt buckets för att undvika att skriva surfpunkter på bekostnad av övergripande läsningsgenomströmning på grund av de ytterligare genomsökningar som behövs för skrivintensiv arbetsbelastning där den primära nyckeln monotont ökar. Dessutom när du använder UPSERT för att skriva ett stort antal poster, inaktivera Committed och batch upp poster.

### <a name="bulk-deletes"></a>Tar bort grupp

När du tar bort en stor datauppsättning, aktivera Committed innan du utfärdar ta borttagningsfrågan, så att klienten inte behöver komma ihåg radnycklar för alla borttagna rader. Committed hindrar klienten från buffring de rader som påverkas av ta bort, så den Phoenix kan ta bort dem direkt på regionservrar utan kostnaden för att återställa dem till klienten.

### <a name="immutable-and-append-only"></a>Oföränderligt och Lägg endast

Om ditt scenario prioriterar skrivhastighet framför dataintegritet, bör du inaktivera write-ahead loggen när du skapar tabeller:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Mer information om den här och andra alternativ finns i [Phoenix grammatik](http://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Nästa steg

* [Phoenix Justeringsguide](https://phoenix.apache.org/tuning_guide.html)
* [Sekundära index](http://phoenix.apache.org/secondary_indexing.html)
