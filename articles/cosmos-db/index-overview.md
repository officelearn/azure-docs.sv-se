---
title: Indexering i Azure Cosmos DB
description: Förstå hur indexering fungerar i Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265703"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexering i Azure Cosmos DB – översikt

Azure Cosmos DB är en schemaoberoende databas och gör att du kan iterera på dina snabbt utan att behöva bry dig om schema- eller indexhantering. Som standard indexerar Azure Cosmos DB automatiskt alla objekt i din behållare utan schema eller sekundära index från utvecklare.

## <a name="items-as-trees"></a>Objekt som träd

Genom projicerar objekt i en behållare som JSON-dokument och som representerar dem som träd, Azure Cosmos DB normaliserar både struktur och instansvärden över objekt i förena konceptet med en **dynamiskt kodad sökvägen** . I den här representation blir varje etikett i ett JSON-dokument som innehåller både egenskapsnamn och deras värden, en nod i trädet. Blad för trädet som innehåller de faktiska värdena och mellanliggande noderna innehålla schemainformationen. Följande bild visar träd som skapats för två objekt (1 och 2) i en Azure Cosmos-behållare:

![Trädrepresentation för två olika objekt i en Azure Cosmos-behållare](./media/index-overview/indexing-as-tree.png)

En pseudo-rotnod skapas som en överordnad till de faktiska noder som motsvarar etiketter i JSON-dokument under. De kapslade datastrukturerna enhet hierarkin i trädet. Mellanliggande artificiella noder som är märkt med numeriska värden (exempelvis 0, 1,...) används för att representera uppräkningar och matrisen index.

## <a name="index-paths"></a>Indexsökvägar

Azure Cosmos DB-projekt objekt i en Azure Cosmos-behållare som JSON-dokument och index som träd. Du kan sedan finjustera index principerna för sökvägar i trädet. Du kan välja att inkludera eller exkludera sökvägar från indexering. Detta kan erbjuda bättre skrivprestanda och sänka index-lagring för scenarier där frågemönstren är kända vidare. Mer information finns i [Index sökvägar](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexering: Under huven

Azure Cosmos-databasen gäller *automatisk indexering* till data, där varje sökväg i ett träd indexeras, såvida du inte konfigurerar för att undanta vissa sökvägar.

Azure Cosmos-databas inverterad index datastruktur för lagring av information för varje objekt och för att underlätta effektiv representation för frågor. Trädet index är ett dokument som har konstruerats med summan av alla träd som representerar enskilda objekt i en behållare. Trädet index ökar med tiden när nya objekt läggs till eller uppdateras befintliga objekt i behållaren. Till skillnad från relationsdatabas indexering, Azure Cosmos DB inte starta om indexering från början, som introduceras nya fält. Nya objekt läggs till i den befintliga index-strukturen. 

Varje nod i trädet index är en indexpost som innehåller värdena etikett och läge, som kallas den *termen*, och ID: N för de objekt som kallas den *anslag*. Anslag inom klammerparenteser (till exempel {1,2}) i vägar i inverterad indextal motsvarar objekt som *Dokument1* och *Dokument2* som innehåller det angivna etikettvärdet. Ett viktigt konsekvenserna av ett enhetligt sätt behandla både schema-etiketter och instansvärden är att allt är packat inuti ett stort index. En Instansvärde som är fortfarande i bladen upprepas inte, det kan vara i olika roller över objekt med olika schemat etiketter, men det är samma värde. Följande bild visar vägar i inverterad indexering för två olika objekt:

![Indexering under huven, inverterad Index](./media/index-overview/inverted-index.png)

> [!NOTE]
> Vägar i inverterad indexet kan se ut ungefär så indexering konstruktioner som används i en sökmotor i informationshämtning-domän. Den här metoden gör kan Azure Cosmos DB du söka i din databas för alla objekt, oavsett dess schema-struktur.

För sökvägen till normaliserade kodar indexet väg framåt hela vägen från roten till-värdet, tillsammans med informationen för värdet. Sökvägen och värdet kodas för att tillhandahålla olika typer av indexering spatial adressintervall, t.ex. Det värde som kodning är utformad att ge unikt värde eller en sammansättning av en uppsättning sökvägar.

## <a name="querying-with-indexes"></a>Frågor med index

Vägar i inverterad indexet kan en fråga för att identifiera de dokument som matchar frågepredikatet snabbt. Vägar i inverterad indexet är också ett träd genom att behandla både schemat och instans värdena ett enhetligt sätt när det gäller sökvägar. Indexet och resultaten kan därför serialiserat till ett giltigt JSON-dokument och returneras som själva dokumenten som de returneras i trädrepresentation. Den här metoden kan recursing över resultat för ytterligare frågor. Följande bild illustrerar ett exempel på indexering i en återställningspunkt-fråga:  

![Exempel på sökfråga punkt](./media/index-overview/index-point-query.png)

För en rad fråga *GermanTax* är en [användardefinierad funktion](stored-procedures-triggers-udfs.md#udfs) körs som en del av frågebearbetning. Den användardefinierade funktionen är alla registrerade, JavaScript-funktion som kan ge omfattande programmeringslogik integreras i frågan. Följande bild illustrerar ett exempel på indexering i en fråga för intervall:

![Exempel på intervallet sökfråga](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Indexeringspolicy](index-policy.md)
- [Indextyper](index-types.md)
- [Indexsökvägar](index-paths.md)
- [Så här hanterar du indexeringsprincip](how-to-manage-indexing-policy.md)
