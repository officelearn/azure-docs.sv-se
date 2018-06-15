---
title: Phoenix prestanda i Azure HDInsight | Microsoft Docs
description: Metodtips för att optimera prestanda för Phoenix.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: b4c1e3fb919ab9ad88a15b51a5e204290a7a12cf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164643"
---
# <a name="phoenix-performance-best-practices"></a>Metodtips för prestanda för Phoenix

Den viktigaste aspekten av Phoenix prestanda är att optimera underliggande HBase. Phoenix skapar en relationsdata modell ovanpå HBase som konverterar SQL-frågor till HBase-åtgärder, till exempel genomsökningar. Design av tabellens schema, markeringen och ordning på fälten i din primära nyckel och din användning av alla index påverka Phoenix prestanda.

## <a name="table-schema-design"></a>Schemat tabelldesign

När du skapar en tabell i Phoenix lagras tabellen i en HBase-tabell. HBase-tabellen innehåller grupper med kolumner (kolumnserier) som används tillsammans. En rad i tabellen Phoenix är en rad i HBase-tabellen, där varje rad består av en ny version celler som associeras med en eller flera kolumner. Logiskt, är en enda rad HBase en samling med nyckel-värdepar med samma rowkey värde. Det vill säga varje nyckel / värde-par har attributet rowkey och värdet för attributet rowkey är densamma för en specifik rad.

Schemat för en tabell med Phoenix finns det primärt nyckeln design, kolumnen family design, enskild kolumn design och hur data är partitionerad.

### <a name="primary-key-design"></a>Primär nyckel design

Den primära nyckeln som definierats i en tabell i Phoenix avgör hur data lagras i rowkey av den underliggande HBase-tabellen. I HBase är det enda sättet att komma åt en viss rad med rowkey. Dessutom kan är data som lagras i ett HBase-tabellen sorterad efter rowkey. Phoenix bygger rowkey värdet genom att sammanbinda värdena för varje kolumn i raden i den ordning som de har definierats i den primära nyckeln.

En tabell för kontakter har till exempel den förnamn, senaste namn, telefonnummer och adress i samma kolumnfamilj. Du kan definiera en primärnyckel som baseras på en ökande sekvensnummer:

|rowkey|       Adress|   telefon| Förnamn| Efternamn|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Om du frågar ofta efter efternamn kan denna primärnyckel dock inte utföra, eftersom varje fråga kräver en fullständig tabellgenomsökning att läsa värdet för varje efternamn. I stället kan du definiera en primärnyckel på lastName, firstName och personnummer kolumner. Sista kolumnen är att undvika tvetydigheten två boende på samma adress med samma namn, till exempel en pappa och son.

|rowkey|       Adress|   telefon| Förnamn| Efternamn| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Med den här nya primärnyckel raden är nycklar som genereras av Phoenix:

|rowkey|       Adress|   telefon| Förnamn| Efternamn| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole John 111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

I den första raden ovan representeras data för rowkey som visas:

|rowkey|       key|   värde| 
|------|--------------------|---|
|  Dole John 111|Adress |1111 San Gabriel Dr.|  
|  Dole John 111|telefon |1-425-000-0002|  
|  Dole John 111|Förnamn |John|  
|  Dole John 111|Efternamn |Dole|  
|  Dole John 111|socialSecurityNum |111| 

Den här rowkey nu lagrar en kopia av data. Överväg att storlek och antalet kolumner som du inkluderar i primärnyckel, eftersom det här värdet ingår i varje cell i den underliggande HBase-tabellen.

Även om den primära nyckeln har värden som monotont stigande, bör du skapa tabell med *salt buckets* till att undvika att skapa skriva surfpunkter - Se [partitionera data](#partition-data).

### <a name="column-family-design"></a>Kolumnen family design

Om vissa kolumner används oftare än andra, bör du skapa flera kolumnserier för att avgränsa kolumnerna som används ofta från sällan används kolumner.

Även om vissa kolumner brukar användas tillsammans, placera dessa kolumner i med samma kolumnfamilj av.

### <a name="column-design"></a>Kolumnen design

* Behåll VARCHAR kolumner under ca 1 MB på grund av i/o-kostnaderna för stora kolumner. Vid bearbetning av frågor, HBase materialiserar celler i sin helhet innan de skickas över till klienten och klienten tar emot dem helt innan du skickar dem till programkoden.
* Lagra kolumnvärdena med hjälp av ett komprimerat format till exempel protobuf Avro, msgpack eller BSON. JSON rekommenderas inte eftersom den är större.
* Överväg att komprimera data före lagring för att klippa ut svarstid och i/o-kostnader.

### <a name="partition-data"></a>Partitionera data

Phoenix kan du kontrollera antalet områden där dina data har distribuerats, vilket ökar prestanda för läsning och skrivning. När du skapar en tabell med Phoenix, kan du antingen salt eller före dela dina data.

Om du vill salt en tabell vid skapandet, anger du antalet salt:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Den här saltning delar tabellen längs värdena för primärnycklar, väljer värden automatiskt. 

För att styra där tabellen delningar inträffar måste dela du före tabellen genom att tillhandahålla intervallvärden längs som uppdelningen inträffar. Till exempel delar om du vill skapa en tabell längs tre områden:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Design för indexet

Ett Phoenix index är en HBase-tabell som innehåller en kopia av vissa eller alla data från tabellen indexerad. Ett index förbättrar prestandan för vissa typer av frågor.

När du har flera index som definierats och fråga en tabell, väljs automatiskt Phoenix bästa indexet för frågan. Det primära indexet har skapats automatiskt baserat på de primära nycklarna som du väljer.

Du kan också skapa sekundärindex genom att ange sina kolumner för förväntade frågor.

När du utformar din index:

* Bara skapa index som du behöver.
* Begränsa antalet index för tabeller som uppdateras ofta. Uppdateringar till en tabell översätta till skrivningar till indexet tabellerna och huvudtabellen.

## <a name="create-secondary-indexes"></a>Skapa sekundärindex

Sekundärindex kan förbättra läsprestanda genom att aktivera vad som skulle vara en fullständig tabellgenomsökning till en punkt-sökning, på bekostnad av lagringsutrymme och skriva hastighet. Sekundärindex kan läggas till eller tas bort när tabellen skulle skapas och inte kräver ändringar av befintliga frågor – frågor körs bara snabbare. Överväg att skapa index som omfattas, funktionella index eller båda beroende på dina behov.

### <a name="use-covered-indexes"></a>Använda skyddad index

Skyddad index är index som innehåller data från raden utöver de värden som indexeras. När du har hittat önskad indexposten behövs det ingen åtkomst till den primära tabellen.

Till exempel kontakta tabell som du kan skapa ett sekundärt index på bara kolumnen socialSecurityNum i exemplet. Sekundär indexet skulle hastigheten som filtrerar enligt socialSecurityNum värden, men hämtar andra fältvärden kräver en annan läsa mot huvudtabellen.

|rowkey|       Adress|   telefon| Förnamn| Efternamn| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole John 111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Om du vanligtvis vill leta upp det förnamn och efternamn som anges i socialSecurityNum kan du skapa ett omfattas index som inkluderar förnamn och efternamn som faktiska data i indextabellen:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Skyddad indexet kan följande fråga för att hämta alla data genom att läsa från den tabell som innehåller sekundärt index:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Använd funktionella index

Funktionella index kan du skapa ett index på ett valfritt uttryck som du förväntar dig att användas i frågor. När du har en fungerande index på plats och en fråga använder uttrycket kan index används för att hämta resultaten i stället för tabellen.

Du kan till exempel skapa ett index så att du kan göra skiftlägeskänsliga sökningar på kombinerade förnamn och efternamn för en person:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Frågans design

De huvudsakliga övervägandena i frågans design är:

* Förstå frågeplanen och kontrollera dess förväntat beteende.
* Anslut effektivt.

### <a name="understand-the-query-plan"></a>Förstå frågeplanen

I [SQLLine](http://sqlline.sourceforge.net/), använda FÖRKLARA följt av SQL-frågan för att visa planen för åtgärder som Phoenix ska utföra. Kontrollera att planen:

* Använder primärnyckel när det är lämpligt.
* Använder lämpliga sekundärindex, i stället för tabellen.
* Använder INTERVALLET SKANNA eller hoppa över SÖKNING när det är möjligt, i stället tabell SKANNA.

#### <a name="plan-examples"></a>Planera exempel

Exempelvis säga att du har en tabell med namnet FLYGPLAN som lagrar information som rör sig fördröjning.

Att välja alla flygplan med en airlineid av `19805`, där airlineid är ett fält som är inte i den primära nyckeln eller i ett index:

    select * from "FLIGHTS" where airlineid = '19805';

Kör kommandot förklaring på följande sätt:

    explain select * from "FLIGHTS" where airlineid = '19805';

Frågeplanen ser ut så här:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Observera frasen fullständig GENOMSÖKNING över FLYGPLAN i den här planen. Frasen anger körningen har SKANNA en tabell över alla rader i tabellen i stället för att använda alternativet effektivare INTERVALLET SKANNA eller hoppa över SÖKNING.

Anta nu kan du vill fråga om flygplan 2 januari 2014 efter en operatör `AA` där dess flightnum var större än 1. Anta att kolumner år, månad, dag i månaden, operatör och flightnum finns i tabellen exempel och ingår i den sammansatta primära nyckeln. Frågan skulle se ut så här:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Låt oss nu undersöka planen för den här frågan med:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Den resulterande planen är:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Värden inom hakparenteser är värdeintervall för de primära nycklarna. I det här fallet av omfångsvärden fasta med år 2014 och månad 1 dag i månaden 2, men tillåter värden för flightnum startas med 2 och på (`*`). Den här frågeplan bekräftar att den primära nyckeln som används som förväntat.

Skapa sedan ett index på FLYGPLAN tabellen med namnet `carrier2_idx` som finns på fältet operatör. Indexet innehåller också flightdate, tailnum, ursprung och flightnum som omfattas kolumner vars data lagras också i indexet.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Anta att du vill hämta en operatör tillsammans med flightdate och tailnum, som i följande fråga:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Du bör se detta index används:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

En fullständig lista över objekt som kan visas i förklarar plan resultat, finns i avsnittet förklarar planer i den [Apache Phoenix justera guiden](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Anslut effektivt

I allmänhet kan vill du undvika kopplingar såvida inte en sida är liten, särskilt på ofta förekommande frågor.

Om nödvändigt, kan du göra stora kopplingar med den `/*+ USE_SORT_MERGE_JOIN */` tips, men en stor koppling är en kostsam åtgärd över stort antal rader. Om den sammanlagda storleken för alla tabeller i right hand sida skulle överskrida det tillgängliga minnet, använder du den `/*+ NO_STAR_JOIN */` tipset.

## <a name="scenarios"></a>Scenarier

Följande riktlinjer beskriver vissa vanliga mönster.

### <a name="read-heavy-workloads"></a>Läs tunga arbetsbelastningar

Läs frekventa användningsfall, kontrollera att du använder index. Dessutom för att spara Läs tid omkostnader, Överväg att skapa index som omfattas.

### <a name="write-heavy-workloads"></a>Skrivintensiv arbetsbelastning

Skapa salt buckets för att undvika att skriva surfpunkter på bekostnad av övergripande läsgenomströmning på grund av ytterligare genomsökningar som behövs för skrivintensiv arbetsbelastning där den primära nyckeln monotont stigande. Dessutom när UPSERT används för att skriva ett stort antal poster kan inaktivera autoCommit och batch upp poster.

### <a name="bulk-deletes"></a>Tar bort grupp

När du tar bort stora datamängder, aktivera autoCommit innan utfärdande ta borttagningsfrågan, så att klienten inte behöver komma ihåg raden nycklar för alla borttagna rader. AutoCommit förhindrar att klienten buffring rader som påverkas av ta bort, så att Phoenix kan ta bort dem direkt på region servrar utan att behöva returnerar dem till klienten.

### <a name="immutable-and-append-only"></a>Ändras och Lägg endast

Om ditt scenario prioriterar skrivåtgärder hastighet framför dataintegritet, bör du inaktivera write-ahead loggen när du skapar tabeller:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Mer information om den här och andra alternativ finns [Phoenix grammatik](http://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Nästa steg

* [Phoenix justera Guide](https://phoenix.apache.org/tuning_guide.html)
* [Sekundärindex](http://phoenix.apache.org/secondary_indexing.html)
