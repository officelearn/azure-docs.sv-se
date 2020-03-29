---
title: Modellering av relationer i Azure Table Storage Design | Microsoft-dokument
description: Förstå modelleringsprocessen när du utformar din tabelllagringslösning.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457560"
---
# <a name="modeling-relationships"></a>Modellera relationer
I den här artikeln beskrivs modelleringsprocessen som hjälper dig att utforma dina Azure Table-lagringslösningar.

Att bygga domänmodeller är ett viktigt steg i utformningen av komplexa system. Vanligtvis använder du modelleringsprocessen för att identifiera entiteter och relationerna mellan dem som ett sätt att förstå affärsdomänen och informera systemets design. Det här avsnittet fokuserar på hur du kan översätta några av de vanliga relationstyperna som finns i domänmodeller till design för table-tjänsten. Processen att mappa från en logisk datamodell till en fysisk NoSQL-baserad datamodell skiljer sig från den som används när en relationsdatabas utformas. Relationsdatabaser design förutsätter vanligtvis en datanormaliseringsprocess optimerad för att minimera redundans - och en deklarativ frågefunktion som abstraherar hur implementeringen av hur databasen fungerar.  

## <a name="one-to-many-relationships"></a>En-till-många-relationer
1:N-relationer mellan affärsdomänobjekt förekommer ofta: till exempel har en avdelning många anställda. Det finns flera sätt att implementera 1:00-relationer i tabelltjänsten var och en med för- och nackdelar som kan vara relevanta för det specifika scenariot.  

Tänk på exemplet med ett stort multinationellt företag med tiotusentals avdelningar och anställda enheter där varje avdelning har många anställda och varje anställd som associeras med en viss avdelning. En metod är att lagra separata avdelnings- och medarbetarenheter som dessa:  


![Lagra separata avdelnings- och medarbetarenheter](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

I det här exemplet visas en implicit 1:1-relation mellan typerna baserat på **partitionsnyckelvärdet.** Varje avdelning kan ha många anställda.  

I det här exemplet visas också en avdelningsentitet och dess relaterade medarbetarentiteter i samma partition. Du kan välja att använda olika partitioner, tabeller eller till och med lagringskonton för de olika entitetstyperna.  

En alternativ metod är att denormalisera dina data och lagra endast medarbetare med denormaliserade avdelningsdata som visas i följande exempel. I det här scenariot kanske den här denormaliserade metoden inte är den bästa om du har ett krav på att kunna ändra information om en avdelningschef eftersom du måste uppdatera alla medarbetare på avdelningen för att göra detta.  

![Medarbetarentitet](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Mer information finns i [denormaliseringsmönstret](table-storage-design-patterns.md#denormalization-pattern) senare i den här guiden.  

I följande tabell sammanfattas för- och nackdelarna för var och en av de metoder som beskrivs ovan för lagring av medarbetare och avdelningsentiteter som har en en-till-många-relation. Du bör också överväga hur ofta du förväntar dig att utföra olika åtgärder: det kan vara acceptabelt att ha en design som innehåller en dyr åtgärd om den åtgärden bara sker sällan.  

<table>
<tr>
<th>Metod</th>
<th>Fördelar</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition, samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelningsentitet med en enda åtgärd.</li>
<li>Du kan använda en EGT för att upprätthålla konsekvens om du har ett krav på att ändra en avdelningsentitet när du uppdaterar/infogar/tar bort en medarbetarentitet. Om du till exempel har ett antal medarbetare på avdelningen för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en medarbetare och en avdelningsenhet för vissa klientaktiviteter.</li>
<li>Lagringsåtgärder sker i samma partition. Vid stora transaktionsvolymer kan detta resultera i en hotspot.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller eller lagringskonton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelningsentitet eller entitet med en enda operation.</li>
<li>Vid höga transaktionsvolymer kan detta hjälpa till att sprida belastningen över fler partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en medarbetare och en avdelningsenhet för vissa klientaktiviteter.</li>
<li>Du kan inte använda EGTs för att upprätthålla konsekvens när du uppdaterar/infogar/tar bort en medarbetare och uppdaterar en avdelning. Till exempel uppdatera ett antal medarbetare i en avdelningsentitet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalisera till enentitetstyp</td>
<td>
<ul>
<li>Du kan hämta all information du behöver med en enda begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyrt att upprätthålla konsekvens om du behöver uppdatera avdelningsinformation (detta skulle kräva att du uppdaterar alla anställda på en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer mellan dessa alternativ, och vilka av för-och nackdelar som är mest betydande, beror på dina specifika programscenarier. Hur ofta ändrar du till exempel avdelningsentiteter. behöver alla dina medarbetarfrågor ytterligare avdelningsinformation. Hur nära är du skalbarhetsgränserna för dina partitioner eller ditt lagringskonto?  

## <a name="one-to-one-relationships"></a>En-till-en-relationer
Domänmodeller kan innehålla en-till-en-relationer mellan entiteter. Om du behöver implementera en 1:1-relation i tabelltjänsten måste du också välja hur du vill länka de två relaterade entiteterna när du behöver hämta dem båda. Den här länken kan antingen vara implicit, baserat på en konvention i nyckelvärdena eller explicit genom att lagra en länk i form av **PartitionKey-** och **RowKey-värden** i varje entitet till dess relaterade entitet. En diskussion om du ska lagra relaterade entiteter i samma partition finns i avsnittet [1:1:1-relationer](#one-to-many-relationships).  

Det finns också implementeringsöverväganden som kan leda till att du implementerar en-till-en-relationer i tabelltjänsten:  

* Hantering av stora entiteter (mer information finns i [Mönster för stora entiteter](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementera åtkomstkontroller (mer information finns i Kontrollera åtkomst med signaturer för delad åtkomst).  

## <a name="join-in-the-client"></a>Gå med i klienten
Även om det finns sätt att modellera relationer i tabelltjänsten bör du inte glömma att de två främsta skälen till att använda tabelltjänsten är skalbarhet och prestanda. Om du upptäcker att du modellerar många relationer som äventyrar lösningens prestanda och skalbarhet bör du fråga dig själv om det är nödvändigt att skapa alla datarelationer i tabelldesignen. Du kanske kan förenkla designen och förbättra lösningens skalbarhet och prestanda om du låter klientprogrammet utföra nödvändiga kopplingar.  

Om du till exempel har små tabeller som innehåller data som inte ändras ofta kan du hämta dessa data en gång och cachelagra dem på klienten. På så sätt kan du undvika upprepade tur- och returturer för att hämta samma data. I de exempel som vi har tittat på i den här guiden, är uppsättningen avdelningar i en liten organisation sannolikt liten och ändra sällan gör det till en bra kandidat för data som klientprogrammet kan ladda ner en gång och cache som slå upp data.  

## <a name="inheritance-relationships"></a>Arvsrelationer
Om klientprogrammet använder en uppsättning klasser som ingår i en arvsrelation för att representera affärsentiteter kan du enkelt spara dessa entiteter i tabelltjänsten. Du kan till exempel ha följande uppsättning klasser definierade i klientprogrammet där **Person** är en abstrakt klass.

![Abstrakt person klass](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Du kan behålla instanser av de två betongklasserna i tabelltjänsten med hjälp av en enda persontabell med entiteter i den där ut ser ut så här:  

![Tabell person](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Mer information om hur du arbetar med flera entitetstyper i samma tabell i klientkoden finns i avsnittet Arbeta med heterogena entitetstyper senare i den här guiden. Detta ger exempel på hur du känner igen entitetstypen i klientkoden.  


## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)
