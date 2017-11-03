---
title: "Hur du arbetar med 'stordata' datakällor | Microsoft Docs"
description: "Artikel syntaxmarkering mönster för att använda Azure Data Catalog med 'stordata' datakällor, inklusive Azure Blob Storage, Azure Data Lake och Hadoop HDFS."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 001d80ce42f0e87276e59d70dffb75eb561d96cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Hur du arbetar med stora datakällor i Azure Data Catalog
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en helt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Det är allt om hjälpa personer identifiera, förstå och använda datakällor och hjälpa organisationer för att få mer värde från deras befintliga datakällor, inklusive stordata.

**Azure Data Catalog** har stöd för registrering av Azure-bloggen Storage-blobbar och samt Hadoop HDFS filer och kataloger. Halvstrukturerade arten av de här datakällorna ger stor flexibilitet. Dock att få ut mesta möjliga värde från registrera dem med **Azure Data Catalog**, användare måste tänka på hur datakällorna som är ordnade.

## <a name="directories-as-logical-data-sets"></a>Kataloger som logiska datauppsättningar
Vanliga mönstret för att ordna stora datakällor är att hantera kataloger som logiska datauppsättningar. Översta kataloger används för att definiera en datamängd, medan undermappar definiera partitioner och deras innehåll lagra själva informationen.

Ett exempel på det här mönstret kan vara:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

I det här exemplet representerar vehicle_maintenance_events och location_tracking_events logiska datauppsättningar. Var och en av mapparna innehåller filer som är ordnad efter år och månad i undermappar. Var och en av dessa mappar kan innehålla hundratals eller tusentals filer.

I det här mönstret registrering av enskilda filer med **Azure Data Catalog** förmodligen inte vara meningsfullt. I stället registreras de kataloger som representerar datamängder som vara meningsfulla för användare som arbetar med data.

## <a name="reference-data-files"></a>Referens-datafiler
En kompletterande mönstret är att lagra referensdatamängder som enskilda filer. Dessa datauppsättningar kan betraktas som ”liten” sida av stordata och liknar ofta dimensioner i en modell med analysdata. Referens-datafiler innehåller poster som används för att ge kontext för flesta datafiler som lagras på annan plats i stordataarkiv.

Ett exempel på det här mönstret kan vara:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

När en analytiker eller data forskare arbetar med data i större katalogstrukturer, kan data i dessa filer användas som ger mer detaljerad information om entiteter som refererar till endast efter namn eller ID i större datamängd.

I det här mönstret är det klokt att registrera enskilda referensfiler data med **Azure Data Catalog**. Representerar en datauppsättning för varje fil och var och en kan kommenterats och identifierade individuellt.

## <a name="alternate-patterns"></a>Alternativa mönster
De mönster som beskrivs i föregående avsnitt är två möjliga sätt en stordataarkiv kan ordnas, men varje implementering är olika. Oavsett hur dina datakällor är strukturerade, när du registrerar ett stort datakällor med **Azure Data Catalog**, fokusera på registrerar de filer och kataloger som representerar datauppsättningar som är av värde till andra i din organisation. Registrerar alla filer och kataloger kan göra katalogen, vilket gör det svårare för användarna att hitta vad de behöver.

## <a name="summary"></a>Sammanfattning
Registrera datakällor med **Azure Data Catalog** gör det lättare att identifiera och förstå. Genom att registrera och kommentera stordata filer och kataloger som representerar logiska datauppsättningar, kan du hjälpa användarna att hitta och använda stort datakällorna som de behöver.
