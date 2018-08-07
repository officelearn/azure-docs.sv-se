---
title: Modellering relationer i Azure storage tabelldesign | Microsoft Docs
description: Förstå modelleringsprocessen när du utformar din lösning för tabellen.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: ed4399e2d58924f89d4201979ffe1fb903a05d0c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522636"
---
# <a name="modeling-relationships"></a>Modellera relationer
Den här artikeln beskrivs modelleringsprocessen för att hjälpa dig att utforma dina lösningar för Azure Table storage.

Modellutveckling domän är ett viktigt steg i utformningen av komplexa system. Normalt använder du modelleringsprocessen för att identifiera enheter och relationer mellan dem som ett sätt att förstå företagsdomänen och informera utformningen av systemet. Det här avsnittet beskrivs hur du kan översätta några vanliga relationstyper hittades i domänmodeller till utformning för Table service. Processen för mappningen från en logisk datamodell till en fysisk NoSQL-baserade-datamodell skiljer sig från det som används när du utformar en relationsdatabas. Relationsdatabaser design förutsätter vanligtvis en process för normalisering av data som optimerats för att minimera redundans – och en deklarativ förfrågningar till funktion som tar fram hur implementeringen av hur databasen fungerar.  

## <a name="one-to-many-relationships"></a>En-till-många-relationer
En-till-många-relationer mellan företag domänobjekt inträffar ofta: till exempel en avdelning har många anställda. Det finns flera sätt att implementera en-till-många-relationer i Table service varje med för- och nackdelar som kan vara relevanta för ett visst scenario.  

Studera exemplet i stora multinationella företag med tiotusentals avdelningar och medarbetare entiteter där alla avdelningar har många anställda och varje medarbetare som som är associerade med en viss avdelning. En metod är att lagra separat avdelning och medarbetare entiteter som dessa:  


![Store separat avdelning och medarbetare entiteter](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Det här exemplet visar en implicit en-till-många-relation mellan de typer som baseras på den **PartitionKey** värde. Varje avdelning kan ha många anställda.  

Det här exemplet visar också en avdelning entitet och entiteter relaterade anställda i samma partition. Du kan välja att använda olika partitioner, tabeller eller även lagringskonton för olika enhetstyper.  

En annan metod är att avnormalisera data och lagra bara medarbetare entiteter med Avnormaliserade avdelningsdata som visas i följande exempel. I det här scenariot kanske med den här Avnormaliserade metoden inte bäst om du har ett krav för att kunna ändra information om en avdelningschef, eftersom om du vill göra detta måste du uppdatera alla medarbetare i avdelningen.  

![Medarbetaren entitet](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Mer information finns i den [Denormalisering mönstret](table-storage-design-patterns.md#denormalization-pattern) senare i den här guiden.  

I följande tabell sammanfattas- och nackdelar med var och en av de metoder som beskrivs ovan för att lagra anställda och avdelning entiteter som har en en-till-många-relation. Du kan också hur ofta du förväntar dig att utföra olika åtgärder: Det kan vara att ha en design som innehåller en kostsam åtgärd om åtgärden endast inträffar sällan.  

<table>
<tr>
<th>Metoden</th>
<th>Experter</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition, samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet med en enda åtgärd.</li>
<li>Du kan använda en EGT för att upprätthålla konsekvens om du har ett krav att ändra en avdelning entitet när du uppdatera/Infoga/Ta bort en medarbetare entitet. Exempel: Om du ha ett antal avdelningens anställda för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Lagringsåtgärder inträffa i samma partition. Vid hög transaktionsvolymer kan detta resultera i en hotspot.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller eller storage-konton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet eller medarbetare enhet med en enda åtgärd.</li>
<li>Vid hög transaktionsvolymer kan detta hjälpa att sprida belastningen över flera partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Du kan inte använda EGTs för att underhålla konsekvensen när du uppdatera/Infoga/Ta bort en medarbetare och uppdatera en avdelning. Till exempel uppdatering av ett antal anställda i en avdelning entitet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Avnormalisera till samma enhetstyp</td>
<td>
<ul>
<li>Du kan hämta all information du behöver med en enskild begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyrt att upprätthålla konsekvens om du vill uppdatera avdelningens information (detta skulle kräva att du uppdaterar alla anställda i en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer mellan dessa alternativ och vilka av för- och nackdelar som är störst, beror på dina specifika Programscenarier. Till exempel hur ofta du ändrar avdelning entiteter; behöver dina medarbetare frågor avdelningens extrainformationen; hur nära kommer du att skalbarhetsgränserna på din partitioner eller ditt storage-konto?  

## <a name="one-to-one-relationships"></a>Motsvarighet
Domänmodeller kan innehålla 1: 1-relationer mellan entiteter. Om du behöver implementera ett förhållande i Table service, måste du också välja hur du länkar de två relaterade entiteterna när du behöver att hämta dem båda. Den här länken kan vara implicit, baserat på en konvention i nyckelvärdena eller explicit genom att lagra en länk i form av **PartitionKey** och **RowKey** värdena i varje entitet till dess relaterad entitet. En diskussion om huruvida du ska lagra relaterade entiteter i samma partition, finns i avsnittet [en-till-många-relationer](#one-to-many-relationships).  

Det finns också tänka på vid implementering som kan leda dig att implementera motsvarighet i Table service:  

* Hantering av stora entiteter (Mer information finns i [stora entiteter mönstret](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementera åtkomstkontroller (Mer information finns i [Kontrollera åtkomst med signaturer för delad åtkomst](#controlling-access-with-shared-access-signatures)).  

## <a name="join-in-the-client"></a>Gå med i klienten
Även om det finns olika sätt att modeller av relationer i Table service, bör du inte glömma att två huvudsakliga skäl till att använda tabelltjänsten finns skalbarhet och prestanda. Om du hittar du modellering många relationer som kan påverka prestanda och skalbarhet för din lösning, bör du be dig själv om det är nödvändigt att skapa alla datarelationer i tabelldesign. Du kan förenkla utformningen och förbättra skalbarhet och prestanda för din lösning om du låter ditt klientprogram som utför alla nödvändiga kopplingar.  

Till exempel om du har liten tabeller som innehåller data som inte ändras ofta, kan sedan du hämta dessa data en gång och cachelagra på klienten. Detta kan undvika upprepade görs för att hämta samma data. I exempel vi har tittat på i den här guiden sannolikt uppsättning avdelningar i en liten organisation att vara liten och ändra sällan att göra det en bra kandidat för data som klientprogram kan ladda ned en gång och cache som söka efter data.  

## <a name="inheritance-relationships"></a>Arvsrelationer
Om klientprogrammet använder en uppsättning klasser som utgör en del av en arvsrelation att representera affärsentiteter, kan du enkelt kan behålla dessa entiteter i Table service. Du kan till exempel ha följande uppsättning klasser som definieras i ditt klientprogram där **Person** är en abstrakt klass.

![Abstrakt klass Person](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Du kan även spara instanser av de två konkreta klasserna i Table service med hjälp av en enskild Person-tabell med entiteter i den ser ut så här:  

![Person-tabell](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Mer information om hur du arbetar med flera typer av enheter i samma tabell i klientkod finns i avsnittet [erfarenhet av heterogena entitetstyper](#working-with-heterogeneous-entity-types) senare i den här guiden. Detta ger exempel på hur du känner igen entitetstypen i klientkod.  


## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabell](table-storage-design-patterns.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Design för dataändringar](table-storage-design-for-modification.md)