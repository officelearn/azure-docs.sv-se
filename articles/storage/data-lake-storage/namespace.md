---
title: Azure Data Lake lagring Gen2 Preview hierarkiska Namespace
description: Beskriver konceptet med hierarkiskt namnområde för Azure Data Lake lagring Gen2 Preview
services: storage
author: jamesbak
manager: twooley
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9b41ca1eedcf69b23557c079e018d69de9fb907c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114375"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure Data Lake lagring Gen2 Preview hierarkiskt namnområde

En nyckel mekanism som gör att Azure Data Lake lagring Gen2 Förhandsgranska för att ge filen systemets prestanda på objektet lagring skala och priser är att lägga till en **hierarkiskt namnområde**. Detta gör att samlingen med objekt/filer i ett konto som ska ordnas i en hierarki med kataloger och kapslade underkataloger på samma sätt som att filsystemet på datorn är organiserat. Med hierarkiskt namnområde aktiverat, ger Data Lake lagring Gen2 skalbarhet och kostnadseffektivitet objektet lagring med filsystemen som analytics motorer och ramverk.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Fördelarna med hierarkiskt namnområde

> [!NOTE]
> Några av funktionerna i listan nedan kan variera i deras tillgänglighet under offentliga förhandsversionen av Azure Data Lake lagring Gen2. När nya funktioner och regioner är tillgängliga under förhandsgranskningsprogrammet, kommer informationen meddelas via vårt dedikerade Yammer-grupp.  

Följande fördelar är associerade med filsystem som implementerar ett hierarkiskt namnområde över blob-data:

- **Atomiska Directory manipulering:** objektet lagrar ungefärlig directory-hierarkin genom användning av en konvention för att bädda in snedstreck (/) i objektnamn att ange sökvägssegment. Denna konvention arbetar för att ordna objekt, ger konventionen inget stöd för åtgärder som flyttar, byta namn på eller ta bort kataloger. Utan verkliga kataloger måste program potentiellt bearbeta miljontals enskilda BLOB att uppnå katalognivå uppgifter. Däremot bearbetar hierarkiskt namnområde dessa uppgifter genom att uppdatera en post (den överordnade katalogen). 

    Denna dramatisk optimering är särskilt viktig för många ramverk för analyser av stordata. Verktyg som Hive, Spark, etc. skriva ofta utdata till tillfällig platser och Byt sedan namn på platsen när jobbet. Utan hierarkiskt namnområde kan det här byta namn ofta ta längre tid än analyserna bearbeta sig själv. Kortare svarstid för jobbet är lika med lägre ägandekostnader (TCO) för analytics-arbetsbelastningar.

- **Bekant gränssnitt format:** filsystem är väl förstådda av utvecklare och användare. Det finns inget behov av att lära dig ett nytt lagringsutrymme paradigmet när du flyttar till molnet som exponeras av Data Lake lagring Gen2 filen systemgränssnittet är samma paradigmet används av datorer, stora och små.

En av skäl att objektet lagrar inte har tidigare stöds hierarkiska namnområden är att hierarkiska namnområden begränsad skala. Data Lake lagring Gen2 hierarkiskt namnområde skalas linjärt och försämrar inte den datakapacitet eller prestanda.

## <a name="when-to-enable-the-hierarchical-namespace"></a>När du ska aktivera hierarkiskt namnområde

Aktivera hierarkiskt namnområde rekommenderas för lagring arbetsbelastningar som är gjorda för filsystem som ändrar kataloger. Detta inkluderar alla arbetsbelastningar som är i första hand för analytics-bearbetning. Datauppsättningar som kräver en hög grad av organisationen får också genom att aktivera hierarkiskt namnområde.

Orsaker till att aktivera hierarkiskt namnområde bestäms av ett TCO, analys. Generellt sett kräver förbättringar av arbetsbelastning svarstiden på grund av lagring acceleration beräkningsresurser för mindre tid. Svarstiden för många arbetsbelastningar kan förbättras på grund av atomiska directory manipulering som är aktiverad som hierarkiskt namnområde. I många arbetsbelastningar beräkningsresursen representerar > 85% av den totala kostnaden och så även en liten minskning av arbetsbelastning svarstiden är lika med mycket TCO besparingar. Även i fall där aktiverar hierarkiskt namnområde ökar kostnader för lagring sänks av TCO fortfarande på grund av minskade beräkning kostnader.

## <a name="when-to-disable-the-hierarchical-namespace"></a>När du ska inaktivera hierarkiskt namnområde

Vissa objekt store arbetsbelastningar kan inte få inga fördelar genom att aktivera hierarkiskt namnområde. Exempel på dessa arbetsbelastningar är säkerhetskopieringar, bildlagring och andra program där objektet organisation lagras separat från själva objekten (*t.ex.*, i en separat databas).

> [!NOTE]
> Med förhandsversionen, om du aktiverar hierarkiskt namnområde finns inga samverkan data eller åtgärder mellan Blob och Data Lake lagring Gen2 REST API. Den här funktionen läggs under förhandsgranskningen.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](./quickstart-create-account.md)