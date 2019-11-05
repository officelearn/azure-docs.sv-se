---
title: Nyheter i tjänsten
titleSuffix: Azure Cognitive Search
description: Meddelanden om nya och förbättrade funktioner, inklusive tjänst byte för Azure Search till Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e81eaf232e3234ac4de0cfb7412e23709f0c0b99
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549102"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Vad är nytt i Azure Kognitiv sökning

Läs om vad som är nytt i tjänsten. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nytt tjänst namn för Azure Search

Azure Search har nu bytt namn till **Azure kognitiv sökning** för att återspegla den utökade användningen av kognitiva kunskaper och AI-bearbetning i kärn åtgärder. Även om kognitiva färdigheter lägger till nya funktioner, är det strikt alternativ att använda AI. Du kan fortsätta att använda Azure Kognitiv sökning utan AI för att skapa omfattande, full texts öknings lösningar över privat, heterogena, textbaserat innehåll i ett index som du skapar och hanterar i molnet. 

API-versioner, NuGet-paket, namnrymder och slut punkter är oförändrade. Dina befintliga Sök lösningar påverkas inte av tjänst namns ändringen.

## <a name="feature-announcements"></a>Funktions aviseringar

4 november 2019-inbrännings konferens

+ [Stegvis indexering](cognitive-search-incremental-indexing-conceptual.md), som nu finns i för hands version, gör att du kan bearbeta eller bearbeta bara de steg som är absolut nödvändiga när du gör ändringar i en anriknings pipeline. Detta är särskilt användbart om du har avbildnings innehåll som du tidigare har analyserat. Utdata från kostsam analys lagras och används sedan som grund för ytterligare indexering eller berikning.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Dokument extrahering](cognitive-search-skill-document-extraction.md) är en kognitiv kunskap som används vid indexering och som gör att du kan extrahera innehållet i en fil inifrån en färdigheter. Tidigare inträffade endast dokument sprickor innan färdigheter kördes. Med att lägga till den här kunskapen kan du också utföra den här åtgärden i färdigheter-körning.

+ [Översättning av text](cognitive-search-skill-text-translation.md) är en kognitiv kunskap som används vid indexering som utvärderar text och, för varje post, returnerar texten översatt till det angivna mål språket.

+ [Power BI mallar](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kan rivstart med visualiseringar och analys av berikat innehåll i ett kunskaps lager på Power BI Desktop. Den här mallen är utformad för Azure Table-projektioner som skapats via [guiden Importera data](knowledge-store-create-portal.md).

## <a name="service-updates"></a>Tjänstuppdateringar

[Tjänst uppdaterings meddelanden](https://azure.microsoft.com/updates/?product=search&status=all) för Azure kognitiv sökning finns på Azure-webbplatsen.