---
title: Azure Marketplace SEO-vägledning | Microsoft Docs
description: Innehåller information om maximera sökmotoroptimering (SEO).
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pbutlerm
ms.openlocfilehash: da7b59400baa4c964dff71fa1f842fede9d68df1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776802"
---
# <a name="azure-marketplace-seo-guidance"></a>Azure Marketplace SEO-vägledning

Den här artikeln förklarar hur du maximerar för att göra ditt erbjudande via sökfunktionen i den [Azure Marketplace](https://azuremarketplace.microsoft.com) och [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Allmän förklaring av algoritmen

Microsofts marknadsplatser använda Azure Search för platsens sökfunktioner. Algoritmen som baseras på termen frekvens – inverterade dokumentet frekvens ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Standard [analysverktyget från Lucene](https://lucene.apache.org/core/) används.

I allmänhet all text fält, kategorier och branscher och ingår i weightage relevanta. Specialiserad termer som används sällan som appar, men ofta i din app genererar en högre poäng matchning med search. Inklusive villkor som ”VM” så skulle erbjuder lite förmånen medan ”Azure search” skulle vara mycket mer specialiserade.
Nedan visas de mest relevanta fälten att tänka på.

 
|  Fält                   | Prioritet | Riktlinjer                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Erbjudandets namn               |  Hög      | Exakt eller nära en fullständig matchning med search frågan kommer att ge hög rangordning.                       |
| Utgivarens namn           |  Hög      | Exakt eller nära en fullständig matchning med search frågan kommer att ge hög rangordning.                       |
| Kort beskrivning        |  Medel    | Beroende namngivning av appar och utgivarens namn nästan garanterar att en hög rangordning, den kanske inte är den mest relevanta. I det här fallet är det viktigt med en kort beskrivning. Behåll texten koncist och sakligt. Nyckelord och förväntade söktermer ska inkluderas för bästa resultat.  Till exempel ”detta är den bästa Retail POS bygger helt på Dynamics 365” är mindre effektiva än ”Retail POS (inköpstillfället) för Dynamics 365”.  | 
| Lång beskrivning         |  Låg       | Beskrivning av erbjuder ett sätt att går in djupare. De mest effektiva beskrivningarna är rimligt långa och nyckelord används.  En to point beskrivningar med hjälp av nyckelord kommer att gynna mer än länge längre text. Se till att viktiga begrepp, till exempel ”IoT”, finns i beskrivningen.  |
| Produktkategorier       | Medel     |  Produktkategorier bestäms av en kombination av alternativ för utgivaren och Microsoft. Välj dessa kategorier på lämpligt sätt så att användarna lätt kan hitta apparna i kategorin korrekt. |
|  |  |  |


## <a name="other-tips"></a>Andra Tips

-   Sök föreslår hämtar tung användaraktivitet. Prioriteras matchningar mot appens namn/utgivare. Kort beskrivning blir nyckelfältet för när söktermen som inte är en exakt matchning med utgivare/appnamn.
-   Dokument för att ladda ned ingår inte i sökningen weightage.
-   Din apps faktiska anskaffning och användning påverkar search rangordning samt. Till exempel får två motsvarande appar där en har avsevärt fler användare en högre rangordning.
