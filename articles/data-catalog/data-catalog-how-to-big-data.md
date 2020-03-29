---
title: Katalogisera stordata i Azure Data Catalog
description: Instruktioner artikel som markerar mönster för att använda Azure Data Catalog med "big data"-datakällor, inklusive Azure Blob Storage, Azure Data Lake och Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 88dc85003fa2a3e41d8a31055ff8ba9b0fcc7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300575"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Katalogisera stordata i Azure Data Catalog

## <a name="introduction"></a>Introduktion

**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Det handlar om att hjälpa människor att upptäcka, förstå och använda datakällor och hjälpa organisationer att få mer värde från sina befintliga datakällor, inklusive stordata.

**Azure Data Catalog** stöder registrering av Azure Blog Storage blobbar och kataloger samt Hadoop HDFS filer och kataloger. Dessa datakällors halvstrukturerade karaktär ger stor flexibilitet. Men för att få mest värde från att registrera dem med **Azure Data Catalog**måste användarna överväga hur datakällorna är organiserade.

## <a name="directories-as-logical-data-sets"></a>Kataloger som logiska datauppsättningar

Ett vanligt mönster för att organisera stordatakällor är att behandla kataloger som logiska datauppsättningar. Kataloger på översta nivån används för att definiera en datauppsättning, medan undermappar definierar partitioner och de filer som de innehåller lagrar själva data.

Ett exempel på det här mönstret kan vara:

```text
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
```

I det här exemplet representerar vehicle_maintenance_events och location_tracking_events logiska datauppsättningar. Var och en av dessa mappar innehåller datafiler som är ordnade efter år och månad i undermappar. Var och en av dessa mappar kan potentiellt innehålla hundratals eller tusentals filer.

I det här mönstret är det förmodligen inte meningsfullt att registrera enskilda filer med **Azure Data Catalog.** Registrera i stället de kataloger som representerar de datauppsättningar som är meningsfulla för användarna som arbetar med data.

## <a name="reference-data-files"></a>Referensdatafiler

Ett kompletterande mönster är att lagra referensdatauppsättningar som enskilda filer. Dessa datamängder kan ses som den "lilla" sidan av stordata, och liknar ofta dimensioner i en analytisk datamodell. Referensdatafiler innehåller poster som används för att ge kontext för huvuddelen av de datafiler som lagras någon annanstans i stordatalagret.

Ett exempel på det här mönstret kan vara:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

När en analytiker eller datavetare arbetar med data som finns i de större katalogstrukturerna kan data i dessa referensfiler användas för att ge mer detaljerad information för entiteter som endast refereras efter namn eller ID i den större datauppsättningen.

I det här mönstret är det vettigt att registrera de enskilda referensdatafilerna med **Azure Data Catalog**. Varje fil representerar en datauppsättning och var och en kan kommenteras och identifieras individuellt.

## <a name="alternate-patterns"></a>Alternativa mönster

De mönster som beskrivs i föregående avsnitt är bara två möjliga sätt att organisera ett stort datalager, men varje implementering är olika. Oavsett hur dina datakällor är strukturerade, när du registrerar stordatakällor med **Azure Data Catalog**, fokusera på att registrera de filer och kataloger som representerar de datauppsättningar som är av värde för andra inom din organisation. Registrera alla filer och kataloger kan röran katalogen, vilket gör det svårare för användarna att hitta vad de behöver.

## <a name="summary"></a>Sammanfattning

Registrera datakällor med **Azure Data Catalog** gör dem lättare att upptäcka och förstå. Genom att registrera och kommentera stordatafiler och kataloger som representerar logiska datauppsättningar kan du hjälpa användarna att hitta och använda de stordatakällor de behöver.
