---
title: Modeling relationer i Azure storage tabelldesign | Microsoft Docs
description: Förstå modelleringsprocessen när du utformar din lagringslösning för tabellen.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 561281375273135009a956fd27dfe9f193ab92ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661070"
---
# <a name="modeling-relationships"></a>Modeling relationer
Den här artikeln beskrivs modelleringsprocessen för att hjälpa dig att utforma din Azure Table-lagringslösningar.

Skapa domänmodeller är ett viktigt steg i utformningen av komplexa system. Normalt använder du modelleringsprocessen för att identifiera enheter och relationer mellan dem som ett sätt att förstå business-domänen och informera designen av systemet. Det här avsnittet fokuserar på hur du kan översätta några av de vanliga relation hittades i domänmodeller för Designer för tabelltjänsten. Processen för mappning från en logisk datamodell till en fysisk NoSQL baserad-datamodell skiljer sig från som används när du skapar en relationsdatabas. Relationsdatabaser design förutsätter vanligtvis en process för normalisering som har optimerats för att minimera redundans – och en deklarativ frågar funktion som avlägsnar hur implementeringen av hur databasen fungerar.  

## <a name="one-to-many-relationships"></a>En-till-många-relationer
En-till-många-relationer mellan företag domänobjekt inträffa ofta: till exempel en avdelning har många anställda. Det finns flera sätt att implementera en-till-många-relationer i tabelltjänsten varje med för- och nackdelar som är relevanta för ett visst scenario.  

Studera exemplet i flera nationella stora företag med tusentals avdelningar och medarbetare enheter där varje avdelning har många anställda och varje medarbetare som tillhör en viss avdelning. En metod är att lagra separat avdelning och medarbetare entiteter som dessa:  


![Lagra separat avdelning och medarbetare entiteter](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Det här exemplet illustrerar en implicit en-till-många-relation mellan de typer som är baserat på den **PartitionKey** värde. Varje avdelning kan ha många anställda.  

Det här exemplet visar också en avdelning entiteten och dess relaterade medarbetare entiteter i samma partition. Du kan välja att använda olika partitioner, tabeller och även lagringskonton för olika enhetstyper.  

En annan metod är att denormalize dina data och lagrar bara medarbetare entiteter med Avnormaliserade avdelningsdata som visas i följande exempel. I det här scenariot kanske kan då Avnormaliserade inte bäst om du har ett krav för att kunna ändra information om en hanterare för avdelning eftersom om du vill göra detta måste du uppdatera alla medarbetare i avdelningen.  

![medarbetare entitet](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Mer information finns i [Denormalization mönster](table-storage-design-patterns.md#denormalization-pattern) senare i den här guiden.  

I följande tabell sammanfattas för- och nackdelarna med vart och ett av de metoder som beskrivs ovan för att lagra medarbetare och avdelningen enheter som har en en-till-många-relation. Det bör också övervägas hur ofta du förväntar dig att utföra olika åtgärder: kan det vara acceptabelt att ha en design som innehåller en kostsam åtgärd om åtgärden endast inträffar sällan.  

<table>
<tr>
<th>Metoden</th>
<th>Tekniker</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition i samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet med en enda åtgärd.</li>
<li>Du kan använda en EGT för att upprätthålla enhetliga om du har ett krav för att ändra en avdelning enhet när du uppdatering/insert/ta bort en medarbetare entitet. Om till exempel underhåll av ett antal avdelningens anställda för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Storage-åtgärder sker i samma partition. Vid höga transaktionsvolymer kan detta resultera i en hotspot.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller storage-konton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet eller medarbetare enhet med en enda åtgärd.</li>
<li>Vid höga transaktionsvolymer att detta sprida belastningen över flera partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Du kan inte använda EGTs för att underhålla konsekvensen när du uppdatering/insert/ta bort en medarbetare och uppdatera en avdelning. Till exempel uppdaterar ett antal anställda i en avdelning entitet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize till samma enhetstyp</td>
<td>
<ul>
<li>Du kan hämta all information du behöver med en enskild begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyra att upprätthålla enhetliga om du behöver uppdatera avdelning information (det här kräver du uppdatera alla anställda i en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer mellan dessa alternativ och vilka av för- och nackdelar som är störst, beror på dina specifika Programscenarier. Till exempel hur ofta du ändrar avdelning entiteter; behöver alla medarbetare frågor kan ytterligare information som avdelningsnivå; hur nära kommer du skalbarhetsgränser på partitioner eller storage-konto?  

## <a name="one-to-one-relationships"></a>1: 1-relationer
Domänmodeller kan innehålla 1: 1-relationer mellan entiteter. Om du behöver implementera en-till-en relation i tabelltjänsten måste du också välja hur du länkar två relaterade entiteter när du behöver hämta dem båda. Den här länken kan vara implicit, baserat på en konvention i nyckelvärdena eller explicit genom att lagra en länk i form av **PartitionKey** och **RowKey** värdena i varje entitet till dess relaterade entitet. Mer information om huruvida du bör lagra relaterade entiteter i samma partition, finns i avsnittet [en-till-många-relationer](#one-to-many-relationships).  

Det finns också implementering som kan leda dig att implementera 1: 1-relationer i tabelltjänsten:  

* Hantering av stora entiteter (Mer information finns i [stora entiteter mönster](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementera åtkomstkontroller (Mer information finns i [Kontrollera åtkomst med signaturer för delad åtkomst](#controlling-access-with-shared-access-signatures)).  

## <a name="join-in-the-client"></a>Delta i klient
Även om det finns olika sätt att modellen relationer i tabelltjänsten, ska du inte glömma att två huvudsakliga skälen för att använda tabelltjänsten finns skalbarhet och prestanda. Om du hittar du modellera många relationer som kan påverka prestanda och skalbarhet i lösningen, bör du fråga dig själv om det är nödvändigt att skapa alla relationer som data i tabelldesign. Du kan förenkla designen och förbättra skalbarhet och prestanda i lösningen om du låta ditt klientprogram som utför alla nödvändiga kopplingar.  

Till exempel om du har liten tabeller som innehåller data som inte ändras ofta kan sedan du hämta dessa data en gång och cachelagras på klienten. Detta kan undvika upprepade görs för att hämta samma data. I exemplen som vi har tittat på i den här handboken sannolikt uppsättning avdelningar i en liten organisation att små och ändra sällan att göra det en bra kandidat för data som klientprogram kan ladda ned en gång och cache som slå upp data.  

## <a name="inheritance-relationships"></a>Arvsrelationer
Om klientprogrammet använder en uppsättning klasser som ingår i en arvsrelation för att representera affärsobjekt, kan du enkelt kvarstår dessa enheter i tabelltjänsten. Du kan till exempel ha följande uppsättning klasser som definieras i ditt klientprogram där **Person** är en abstrakt klass.

![Abstrakt klass för Person](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Du kan spara instanser av två konkreta klasser i tjänsten tabellen med hjälp av en enskild Person tabell med entiteter i den ser ut så här:  

![Person tabell](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Mer information om hur du arbetar med flera typer av enheter i samma tabell i klientkod finns i avsnittet [arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types) senare i den här guiden. Detta ger exempel på hur du identifierar entitetstypen i klientkod.  


## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabellen](table-storage-design-patterns.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Design för dataändring](table-storage-design-for-modification.md)