---
title: Azure Data Source guiden för Azure Machine Learning | Microsoft Docs
description: Förklarar guiden Datakälla AML-arbetsstationen
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: c74229504a43179673cc99ccff321b65e3f6ed4f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650298"
---
# <a name="data-source-wizard"></a>Guiden datakälla #

Guiden datakälla är ett snabbt och enkelt sätt att sätta en datauppsättning i Azure ML Workbench utan kod. Det är där du kan också välja en exempel-strategi för datauppsättningen och datatyper för varje kolumn. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Steg 1: Aktivera guiden datakälla ## 

Hämta data till ett projekt med hjälp av guiden datakälla. Välj den **+** knappen bredvid sökrutan i datavyn och väljer Lägg till datakälla. 

![Lägg till datakälla](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Steg 2: Välj där data lagras ##
Först måste du ange hur dina data är för närvarande i. Det kan lagras i en platt fil eller en katalog, en parquet-fil, en Excel-fil eller en databas. Mer information finns i [stöds datakällor](data-prep-appendix2-supported-data-sources.md).

![Steg 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Steg 3: Välj datafilen ##
Ange sökvägen till filen för en filen eller katalogen. Välj i listrutan plats för data – det kan vara en lokal filsökväg eller Azure Blob Storage. 

Ange sökvägen genom att skriva det eller om du klickar på den **Bläddra...** knappen för att söka efter den. Du kan bläddra efter en katalog eller en eller flera filer.

Klicka på **Slutför** att acceptera standardinställningarna för resten av stegen eller nästa för att gå vidare till nästa steg.


![Steg 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Steg 4: Välj filparametrar ##

Guiden datakälla kan automatiskt identifiera filen typ, avgränsare och kodning. Det skulle också visas ett exempel på hur data ser ut. Du kan också ändra någon av dessa parametrar manuellt. 

![Steg 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Steg 5: Ange datatyper för kolumner ##

Guiden Datakälla identifierar automatiskt datatyperna för datauppsättningens kolumner. Om den missar något, eller om du vill framtvinga en datatyp, kan du manuellt ändra datatypen. Den **utdata EXEMPELDATA** kolumnen visar exempel på hur data ska se ut.

För kolumner som Data Prep härleder för att innehålla datum, kan du uppmanas att välja ordningen på månad och dag i datumformatet. Exempel: 1/2/2013 kan vara 2 januari (för detta, Välj *Dagarsmånad*) eller Feburary 1: a (Välj *månad-dag*).

![Steg 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Steg 6: Välj samplingsstrategi för data ##

Du kan ange en eller flera sampling strategier för datauppsättningen och välj något som aktiv strategi. Standardvärdet är att läsa in Top 10000 rader. Du kan redigera det här exemplet genom att klicka på den **redigera** i verktygsfältet eller lägga till en ny strategi genom att klicka på + ny. De strategier som för närvarande stöder är

-     Översta antalet rader
-     Slumpmässigt antal rader
-     Slumpmässig procentandelen rader
-     Fullständig fil

Du kan ange så många sampling strategier som du vill, men det finns bara ett som kan ställas in till aktiv när du förbereder dina data. Du kan ange eventuella strategi för att vara aktiv genom att välja vilken strategi och klicka på Ange som aktiv i verktygsfältet.

Beroende på varifrån data kommer från, stöds några exempel strategier inte. Läs mer om sampling, titta på avsnittet sampling i [det här dokumentet](data-prep-user-guide.md) 

![Steg 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Steg 7: Sökväg kolumnen hantering ##

Om sökvägen innehåller viktiga data, kan du inkludera den som den första kolumnen i datauppsättningen. Det här alternativet är användbart om du tar med i flera filer. Du kan också välja att inte inkludera den.

![Steg 7](media/data-source-wizard/step6.png)

När du klickar på Slutför läggs en ny datakälla till i projektet. Du hittar den under gruppen datakällor i datavyn eller som en dsource-fil i den **Filvyn**.
