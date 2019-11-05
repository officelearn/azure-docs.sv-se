---
title: Datatransformering data flöden i Azure Data Factory | Microsoft Docs
description: En översikt över datatransformering data flöden i Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 05119adfa5b254297fd656854a027ede05c5ef26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518585"
---
# <a name="what-are-wrangling-data-flows"></a>Vad är datatransformering data flöden?

Organisationer behöver utföra förberedelse av data och datatransformering för korrekt analys av komplexa data som fortsätter att växa varje dag. Förberedelse av data krävs så att organisationer kan använda data i olika affärs processer och minska tiden till värde.

Med datatransformering data flöden i Azure Data Factory kan du göra kod fria data förberedelser i moln skala upprepade gånger. Datatransformering data flöden integreras med [Power Query online](https://docs.microsoft.com/power-query/) och gör Power Query M-funktioner tillgängliga för Data Factory-användare.

Datatransformering Data Flow översätter M som genereras av Power Query online mashup-redigeraren till Spark-kod för moln skalnings körning.

Datatransformering data flöden är särskilt användbara för data tekniker eller "medborgarnas data integratorer".

## <a name="use-cases"></a>Användningsfall

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snabb interaktiv data utforskning och förberedelse

Flera data tekniker och medborgarnas Data Integrator kan interaktivt utforska och förbereda data uppsättningar i moln skala. Med ökningen av volym, variation och hastighet av data i data sjöar behöver användarna ett effektivt sätt att utforska och förbereda data uppsättningar. Du kan till exempel behöva skapa en data uppsättning som har all kund demografisk information för nya kunder sedan 2017. Du mappar inte till ett känt mål. Du är utforska, datatransformering och förberedelser data uppsättningar för att uppfylla ett krav innan du publicerar det i sjön. Datatransformering data flöden används ofta för mindre formella analys scenarier. Beredd-datauppsättningarna kan användas för att utföra omvandlingar och maskin inlärnings åtgärder.

### <a name="code-free-agile-data-preparation"></a>Kod – kostnads fri förberedelse av smidig data behandling

Medborgarnas data integratorer ägnar mer än 60% av tiden att söka efter och förbereda data. De vill göra det med en kod gratis för att förbättra drift produktiviteten. Genom att tillåta att medborgarna integrerar, formar och publicerar data med hjälp av kända verktyg som Power Query online på ett skalbart sätt förbättrar produktiviteten avsevärt. Datatransformering data flöde i Azure Data Factory gör att den välbekanta Power Querys mashup-redigeraren kan göra det möjligt för medborgarna att åtgärda fel snabbt, standardisera data och skapa data med hög kvalitet för att stödja affärs beslut.

### <a name="data-validation"></a>Data verifiering

Genomsök data visuellt i ett kod fritt sätt för att ta bort eventuella avvikare, avvikelser och anpassa dem till en form för snabb analys.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett datatransformering-dataflöde](wrangling-data-flow-tutorial.md).