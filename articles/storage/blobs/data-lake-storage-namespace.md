---
title: Hierarkiskt namnområde för Azure Data Lake Storage Gen2
description: Beskriver konceptet med ett hierarkiskt namnområde för Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153085"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 hierarkiskt namnområde

En nyckelmekanism som gör att Azure Data Lake Storage Gen2 kan ge filsystemprestanda vid objektlagringsskala och priser är tillägget av ett **hierarkiskt namnområde**. På så sätt kan insamlingen av objekt/filer i ett konto ordnas i en hierarki av kataloger och kapslade underkataloger på samma sätt som filsystemet på datorn är organiserat. Med ett hierarkiskt namnområde aktiverat kan ett lagringskonto ge skalbarhet och kostnadseffektivitet för objektlagring, med filsystemssenmantik som är välbekanta för analysmotorer och ramverk.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Fördelarna med ett hierarkiskt namnområde

Följande fördelar är associerade med filsystem som implementerar ett hierarkiskt namnområde över blob-data:

- **Atomär katalog manipulation:** Objektet lagrar ungefär en kataloghierarki genom att anta en konvention om inbäddning av snedstreck (/) i objektnamnet för att beteckna sökvägssegment. Även om den här konventionen fungerar för att organisera objekt, ger konventionen ingen hjälp för åtgärder som att flytta, byta namn på eller ta bort kataloger. Utan riktiga kataloger måste program bearbeta potentiellt miljontals enskilda blobbar för att uppnå uppgifter på katalognivå. Ett hierarkiskt namnområde bearbetar däremot dessa uppgifter genom att uppdatera en enda post (den överordnade katalogen).

    Denna dramatiska optimering är särskilt viktig för många ramverk för stordataanalys. Verktyg som Hive, Spark, etc. skriver ofta utdata till tillfälliga platser och byter sedan namn på platsen när jobbet är avslutat. Utan ett hierarkiskt namnområde kan det här namnet ofta ta längre tid än själva analysprocessen. Lägre jobbfördröjning är lika med lägre total ägandekostnad (TCO) för analysarbetsbelastningar.

- **Välbekanta gränssnitt stil:** Filsystem är väl förstås av utvecklare och användare. Det finns ingen anledning att lära sig ett nytt lagringsparadigm när du flyttar till molnet eftersom filsystemgränssnittet som exponeras av Data Lake Storage Gen2 är samma paradigm som används av datorer, stora som små.

En av anledningarna till att objektlager inte historiskt har stöd för ett hierarkiskt namnområde är att ett hierarkiskt namnområde begränsar skalan. Hierarkiska namnområde datasjölagring gen2 skalas dock linjärt och försämrar inte vare sig datakapaciteten eller prestandan.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Bestämma om ett hierarkiskt namnområde ska aktiveras

När du har aktiverat ett hierarkiskt namnområde för ditt konto kan du inte återställa det till ett platt namnområde. Tänk därför på om det är meningsfullt att aktivera ett hierarkiskt namnområde baserat på vilken typ av arbetsbelastningar för objektlagringen är.

Vissa arbetsbelastningar kanske inte får någon fördel genom att aktivera ett hierarkiskt namnområde. Exempel på detta är säkerhetskopior, bildlagring och andra program där objektorganisationen lagras separat från själva objekten (till exempel i en separat databas). 

Även om stödet för Blob-lagringsfunktioner och Azure-tjänstekosystemet fortsätter att växa, finns det fortfarande vissa funktioner och Azure-tjänster som ännu inte stöds i konton som har ett hierarkiskt namnområde. Se [kända problem](data-lake-storage-known-issues.md). 

I allmänhet rekommenderar vi att du aktiverar ett hierarkiskt namnområde för lagringsarbetsbelastningar som är utformade för filsystem som manipulerar kataloger. Detta inkluderar alla arbetsbelastningar som främst är för analysbearbetning. Datauppsättningar som kräver en hög grad av organisation kommer också att gynnas genom att aktivera ett hierarkiskt namnområde.

Orsakerna till att aktivera ett hierarkiskt namnområde bestäms av en TCO-analys. Generellt sett kräver förbättringar i arbetsbelastningsfördröjning på grund av lagringsacceleration beräkningsresurser för kortare tid. Svarstid för många arbetsbelastningar kan förbättras på grund av atomkatalogmanipulering som aktiveras av ett hierarkiskt namnområde. I många arbetsbelastningar representerar beräkningsresursen > 85 % av den totala kostnaden och därför motsvarar även en blygsam minskning av svarstiden för arbetsbelastningen en betydande mängd TCO-besparingar. Även i de fall där aktivering av ett hierarkiskt namnområde ökar lagringskostnaderna sänks TCO fortfarande på grund av minskade beräkningskostnader.

Information om hur du analyserar skillnader i priser för datalagring, transaktionspriser och prissättning för lagringskapacitetsreservation mellan konton som har ett platt hierarkiskt namnområde jämfört med ett hierarkiskt namnområde finns i [Azure Data Lake Storage Gen2-priser](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](./data-lake-storage-quickstart-create-account.md)
