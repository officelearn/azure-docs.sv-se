---
title: Azure Data Lake Storage Gen2 förhandsversion hierarkisk Namespace
description: Beskriver konceptet med hierarkiskt namnområde för Azure Data Lake Storage Gen2 förhandsversion
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 967e24ae6e004fe6ce2b1c0aa6c039f46be2598c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244512"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 förhandsversion hierarkiskt namnområde

En viktig mekanism som gör att Azure Data Lake Storage Gen2 förhandsversion att ge filen systemets prestanda i skala för lagring av objekt och priser är att lägga till en **hierarkiskt namnområde**. Detta möjliggör insamling av objekt/filer inom ett konto som ska ordnas data i en hierarki med kataloger och kapslade underkataloger på samma sätt som att filsystemet på datorn organiseras. Med den hierarkiskt namnområde aktiverat, blir ett lagringskonto kan tillhandahålla den skalbarhet och kostnadseffektiviteten för lagring av objekt med filsystemen som känner igen analytics motorer och ramverk.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Fördelarna med hierarkiskt namnområde

Följande fördelar är kopplade till filsystem som implementerar ett hierarkiskt namnområde över blob-data:

- **Manipulering av atomiska directory:** Objektlager ungefär en directory-hierarkin genom att använda en konvention för inbäddning snedstreck (/) i objektnamn att ange sökvägssegment. Även om denna konvention fungerar för att ordna objekt, tillhandahåller konventionen utan hjälp för åtgärder som att flytta, byta namn på eller ta bort kataloger. Utan verkliga kataloger måste program potentiellt bearbeta miljontals enskilda BLOB-och uppnå katalognivå uppgifter. Däremot bearbetar hierarkiskt namnområde dessa uppgifter genom att uppdatera en enda post (den överordnade katalogen).

    Den här dramatisk optimering är särskilt betydelsefull för många ramverk för analys av stordata. Verktyg som Hive, Spark, etc. skriva ofta utdata till tillfällig platser och Byt sedan namn på plats när jobbet. Utan hierarkiskt namnområde kan den här Byt namn på ofta ta längre tid än analytics bearbeta själva. Kortare svarstider för jobbet är lika med lägre totala ägandekostnad (TCO) för analytiska arbetsbelastningar för.

- **Välbekanta gränssnitt format:** Filsystem är väl förstådda av utvecklare och användare. Det finns inget behov att lära dig en ny storage-paradigm när du flyttar till molnet som exponeras av Data Lake Storage Gen2 file systemgränssnittet är samma paradigmet används av datorer, stora och små.

En av anledningarna att objektlager inte har historiskt stöds hierarkisk namnområden är att hierarkisk namnområden begränsad skala. Data Lake Storage Gen2 hierarkiskt namnområde skalas linjärt och försämras inte antingen datakapacitet eller prestanda.

## <a name="when-to-enable-the-hierarchical-namespace"></a>När du ska aktivera hierarkiskt namnområde

Aktivera hierarkiskt namnområde rekommenderas för storage-arbetsbelastningar som är utformade för filsystem som manipulerar kataloger. Detta inkluderar alla arbetsbelastningar som är främst för bearbetning av trafikanalys. Datauppsättningar som kräver en hög grad av organisation kan också dra genom att aktivera hierarkiskt namnområde.

Skäl för att aktivera hierarkiskt namnområde bestäms av en TCO-analys. Generellt sett kräver förbättringar i arbetsbelastningen fördröjning på grund av storage acceleration beräkningsresurser för kortare tid. Svarstid för många arbetsbelastningar kan förbättras på grund av manipulering av atomiska directory som är aktiverad som hierarkiskt namnområde. I många arbetsbelastningar beräkningsresursen representerar > 85% av den totala kostnaden och så även en mycket små minskning av arbetsbelastningen svarstiden motsvarar en betydande mängd TCO-besparingar. Även i fall där om du aktiverar hierarkiskt namnområde ökar kostnader för lagring sänkte av TCO fortfarande på grund av lägre beräkningskostnader.

## <a name="when-to-disable-the-hierarchical-namespace"></a>När du ska inaktivera hierarkiskt namnområde

Vissa objekt store arbetsbelastningar kan inte få någon förmån genom att aktivera hierarkiskt namnområde. Exempel på dessa arbetsbelastningar är säkerhetskopior, bildlagring och andra program där objektet organisation lagras separat från själva objektet (*t.ex.*, i en separat databas).


## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](./data-lake-storage-quickstart-create-account.md)
