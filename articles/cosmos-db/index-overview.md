---
title: Indexering i Azure Cosmos DB
description: Förstå hur indexering fungerar i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 9e1ae9faecea67e1abb2a1b08d5641bc25c9f38a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469098"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexering i Azure Cosmos DB – översikt

Azure Cosmos DB är en schemaoberoende databas och gör att du kan iterera på dina snabbt utan att behöva bry dig om schema- eller indexhantering. Som standard indexerar Azure Cosmos DB automatiskt alla objekt i din behållare utan schema eller sekundära index från utvecklare.

## <a name="items-as-trees"></a>Objekt som träd

Genom projicerar objekt i en behållare som JSON-dokument och som representerar dem som träd, Azure Cosmos DB normaliserar både struktur och instansvärden över objekt i förena konceptet med en **dynamiskt kodad sökvägen** . I den här representation blir en nod i trädet i varje etikett i ett JSON-dokument som innehåller både egenskapsnamn och deras värden. Blad för trädet innehåller faktiska värden och mellanliggande noderna innehåller schemainformationen. Följande bild visar träd som skapats för två objekt (1 och 2) i en behållare:

![Trädrepresentation för två olika objekt i en Azure Cosmos-behållare](./media/index-overview/indexing-as-tree.png)

En pseudo-rotnod skapas som en överordnad till de faktiska noder för etiketter i dokumentet under. De kapslade datastrukturerna enhet hierarkin i trädet. Mellanliggande artificiella noder som är märkt med numeriska värden (exempelvis 0, 1,...) används för att representera uppräkningar och matrisen index.

## <a name="index-paths"></a>Indexsökvägar

Azure Cosmos DB-projekt objekt som JSON-dokument och index som träd. Du kan sedan finjustera för principer för sökvägar i trädet. Du kan välja att inkludera eller exkludera sökvägar från indexering. Detta kan erbjuda bättre skrivprestanda och sänka index-lagring för scenarier där frågemönstren är kända vidare. Mer information finns i [Index sökvägar](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexering: Under huven

Azure Cosmos-databasen gäller automatisk indexering till data, där varje sökväg i ett träd indexeras såvida du inte konfigurerar för att undanta vissa sökvägar.

Azure Cosmos-databas inverterad index datastruktur för lagring av information för varje objekt och för att underlätta effektiv representation för frågor. Trädet index är ett dokument som har konstruerats med summan av alla träd som representerar enskilda objekt i behållaren. Index-trädet växer med tiden när nya objekt läggs till eller uppdateras befintliga objekt i behållaren. Till skillnad från relationsdatabas indexering läggs Azure Cosmos DB inte starta om indexering från grunden när nya fält introduceras nya objekt till i den befintliga strukturen. 

Varje nod i trädet index är en indexpost som innehåller de etikett och läge värden, så kallade på sikt och ID: n för de objekt som kallas anslag. Anslag inom klammerparenteser (till exempel {1,2}) i vägar i inverterad indextal motsvarar objekt, till exempel Dokument1 och Dokument2 som innehåller det angivna etikettvärdet. Ett viktigt konsekvenserna av ett enhetligt sätt behandla både schema-etiketter och instansvärden är att allt är packat inuti ett stort index. En Instansvärde som är fortfarande i bladen upprepas inte, det kan vara i olika roller över objekt med olika schemat etiketter, men det är samma värde. Följande bild visar vägar i inverterad indexering för olika objekt:

![Indexering under huven, inverterad Index](./media/index-overview/inverted-index.png)

> [!NOTE]
> Vägar i inverterad indexet kan se ut ungefär så indexering konstruktioner som används i en sökmotor i informationshämtning-domän. Den här metoden gör kan Azure Cosmos DB du söka i din databas för alla objekt, oavsett dess schema-struktur.

För sökvägen till normaliserade kodar indexet väg framåt hela vägen från roten till-värdet, tillsammans med informationen för värdet. Sökvägen och värdet kodas för att tillhandahålla olika typer av indexering till exempel intervall, spatial typer. Det värde som kodning är utformad att ge unikt värde eller en sammansättning av en uppsättning sökvägar.

## <a name="querying-with-indexes"></a>Frågor med index

Vägar i inverterad indexet kan en fråga för att identifiera de dokument som matchar frågepredikatet snabbt. Vägar i inverterad indexet är också ett träd genom att behandla både schema- och instans värden ett enhetligt sätt när det gäller sökvägar. Indexet och resultaten kan därför serialiserat till ett giltigt JSON-dokument och returneras som själva dokumenten som de returneras i trädrepresentation. Den här metoden kan recursing över resultat för ytterligare frågor. Följande bild illustrerar ett exempel på indexering i en återställningspunkt-fråga:  

![Exempel på sökfråga punkt](./media/index-overview/index-point-query.png)

För en fråga för intervallet är GermanTax en användardefinierad funktion som körs som en del av frågebearbetning. Den användardefinierade funktionen är alla registrerade, Javascript-funktion som kan ge omfattande programmeringslogik integreras i frågan. Följande bild illustrerar ett exempel på indexering i en fråga för intervall:

![Exempel på intervallet sökfråga](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Indexeringspolicy](index-policy.md)
- [Indextyper](index-types.md)
- [Indexsökvägar](index-paths.md)
- [Så här hanterar du indexeringsprincip](how-to-manage-indexing-policy.md)
