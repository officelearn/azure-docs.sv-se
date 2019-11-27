---
title: Nya funktions meddelanden
titleSuffix: Azure Cognitive Search
description: Meddelanden om nya och förbättrade funktioner, inklusive tjänst byte för Azure Search till Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f35fcc2604866c2ff5a330b65ae844be0eed9476
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422392"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Vad är nytt i Azure Kognitiv sökning

Läs om vad som är nytt i tjänsten. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nytt tjänst namn för Azure Search

Azure Search har nu bytt namn till **Azure kognitiv sökning** för att återspegla den utökade användningen av kognitiva kunskaper och AI-bearbetning i kärn åtgärder. Även om kognitiva färdigheter lägger till nya funktioner, är det strikt alternativ att använda AI. Du kan fortsätta att använda Azure Kognitiv sökning utan AI för att skapa omfattande, full texts öknings lösningar över privat, heterogena, textbaserat innehåll i ett index som du skapar och hanterar i molnet. 

API-versioner, NuGet-paket, namnrymder och slut punkter är oförändrade. Dina befintliga Sök lösningar påverkas inte av tjänst namns ändringen.

## <a name="feature-announcements"></a>Funktions aviseringar

4 november 2019-inbrännings konferens

+ Med [stegvis indexering (för hands version)](cognitive-search-incremental-indexing-conceptual.md) kan du välja och välja vilka steg som ska utföras när du gör ändringar i en berikad pipeline. Stegvis indexering är användbart om du har bild innehåll som du tidigare har analyserat. Utdata från kostsam analys lagras och används sedan som grund för ytterligare indexering eller berikning.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrahering av dokument (för hands version)](cognitive-search-skill-document-extraction.md) är en kognitiv kunskap som används vid indexering och som gör att du kan extrahera innehållet i en fil från en färdigheter. Tidigare inträffade endast dokument sprickor innan färdigheter kördes. Med att lägga till den här kunskapen kan du också utföra den här åtgärden i färdigheter-körning.

+ [Text översättning (för hands version)](cognitive-search-skill-text-translation.md) är en kognitiv färdighet som används vid indexering som utvärderar text och, för varje post, returnerar texten översatt till det angivna mål språket.

+ [Power BI mallar](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kan rivstart med visualiseringar och analys av berikat innehåll i ett kunskaps lager på Power BI Desktop. Den här mallen är utformad för Azure Table-projektioner som skapats via [guiden Importera data](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (för hands version)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (för hands version)](search-howto-index-cosmosdb.md)och [Cosmos DB API för Cassandra (för hands version)](search-howto-index-cosmosdb.md) stöds nu i indexerare. Du kan registrera dig med [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). Du får ett bekräftelse meddelande när du har accepterat i för hands versions programmet.

Juli 24 2019

+ Allmänt tillgänglig i [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Tjänstuppdateringar

[Tjänst uppdaterings meddelanden](https://azure.microsoft.com/updates/?product=search&status=all) för Azure kognitiv sökning finns på Azure-webbplatsen.