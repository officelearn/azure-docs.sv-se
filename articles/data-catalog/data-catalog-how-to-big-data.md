---
title: Hur du arbetar med ”stordata” datakällor i Azure Data Catalog
description: Artikel markering mönster för att använda Azure Data Catalog med ”stordata” datakällor, inklusive Azure Blob Storage, Azure Data Lake och Hadoop HDFS.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 42b002e3494d84084979f2f5b27e9679e61cbeb5
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407730"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Arbeta med stordatakällor i Azure Data Catalog
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för företagsdatakällor. Det är allt om hur personer identifiera, förstå och använda datakällor och att hjälpa organisationer för att få ut mer av sina befintliga datakällor, inklusive stordata.

**Azure Data Catalog** har stöd för registrering av Azure Blog Storage-blobbar och samt HDFS Hadoop-filer och kataloger. Halvstrukturerade natur datakällorna ger stor flexibilitet. Men att få ut mest värde från att registrera dem med **Azure Data Catalog**, användare måste ta hänsyn till hur datakällorna organiseras.

## <a name="directories-as-logical-data-sets"></a>Kataloger som logiska datauppsättningar
Ett vanligt mönster för att ordna stordatakällor är att hantera kataloger som logiska datauppsättningar. Översta kataloger används för att definiera en datamängd, medan undermappar definiera partitioner och deras innehåll lagra själva informationen.

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

I det här exemplet representerar vehicle_maintenance_events och location_tracking_events logiska datauppsättningar. Var och en av mapparna innehåller filer som är ordnade efter år och månad i undermappar. Var och en av dessa mappar kan innehålla hundratals eller tusentals filer.

I det här mönstret registrerar enskilda filer med **Azure Data Catalog** förmodligen inte är användbar. I stället att registrera de kataloger som representerar de datauppsättningar som används för att vara meningsfulla för de användare som arbetar med data.

## <a name="reference-data-files"></a>Referens-datafiler
Ett kompletterande mönster är att lagra referensdatauppsättningar som enskilda filer. Dessa datauppsättningar kan betraktas som ”små” sida av stordata och är ofta liknar dimensioner i en modell för analysdata. Referens för datafilerna innehåller poster som används för att tillhandahålla kontexten för stora mängd datafiler lagrade någon annanstans i arkivet för stordata.

Ett exempel på det här mönstret kan vara:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

När en analytiker eller data Science arbetar med data i större katalogstrukturer, kan du använda data i dessa referensfiler att ge mer detaljerad information för entiteter som refererar till endast efter namn eller ID i större datamängd.

I det här mönstret är det praktiskt att registrera referens för enskilda datafilerna med **Azure Data Catalog**. Varje fil som representerar en uppsättning data och var och en kan kommenterade och identifierade individuellt.

## <a name="alternate-patterns"></a>Alternativa mönster
De mönster som beskrivs i föregående avsnitt finns två möjliga sätt lagringsplatser för stordata kan ordnas, men varje implementering är olika. Oavsett hur dina datakällor är strukturerade, när du registrerar stordatakällor med **Azure Data Catalog**, fokusera på registrerar de filer och kataloger som representerar de datauppsättningar som är värdefulla med andra inom din organisation. Registrera alla filer och kataloger kan störa katalogen, vilket gör det svårt för användarna att hitta vad de behöver.

## <a name="summary"></a>Sammanfattning
Registrera datakällor med **Azure Data Catalog** gör det enklare att identifiera och förstå. Genom att registrera och kommentera stordata filer och kataloger som representerar logiska datauppsättningar, kan du hjälpa användarna att hitta och använda stordatakällor som de behöver.
