---
title: Hantera felrader med mappningsdataflöden i Azure Data Factory
description: Lär dig hur du hanterar SQL-trunkeringsfel i Azure Data Factory med hjälp av mappningsdataflöden.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73166543"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Hantera SQL-trunkeringsfelrader i dataflöden för datamappning av dataflöden för data

Ett vanligt scenario i Data Factory när du använder mappning av dataflöden är att skriva dina transformerade data till en Azure SQL-databas. I det här fallet är ett vanligt feltillstånd som du måste förhindra mot möjlig kolumn trunkering. Följ dessa steg för att tillhandahålla loggning av kolumner som inte får plats i en målsträngskolumn, så att dataflödet kan fortsätta i dessa scenarier.

## <a name="scenario"></a>Scenario

1. Vi har en mål-Azure SQL-databastabell som har en ```nvarchar(5)``` kolumn som heter "namn".

2. Inuti vårt dataflöde vill vi kartlägga filmtitlar från vår diskbänk till den målkolumnen för "namn".

    ![Dataflöde för film 1](media/data-flow/error4.png)
    
3. Problemet är att filmtiteln inte alla får plats i en sinkkolumn som bara rymmer fem tecken. När du kör det här dataflödet visas ett fel som det här:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Hur man utformar runt detta villkor

1. I det här fallet är den maximala längden på kolumnen "namn" fem tecken. Så, låt oss lägga till en villkorlig delad omvandling som gör det möjligt för oss att logga rader med "titlar" som är längre än fem tecken samtidigt som resten av raderna som kan passa in i det utrymmet för att skriva till databasen.

    ![villkorlig delning](media/data-flow/error1.png)

2. Den här villkorliga delningsomformningen definierar den maximala längden på "titel" som fem. Alla rader som är mindre än eller ```GoodRows``` lika med fem kommer att gå in i strömmen. Varje rad som är större ```BadRows``` än fem kommer att gå in i strömmen.

3. Nu måste vi logga raderna som misslyckades. Lägg till en ```BadRows``` sink-omformning i flödet för loggning. Här kommer vi att "mappa automatiskt" alla fält så att vi har loggning av hela transaktionsposten. Detta är en textavgränsad CSV-filutdata till en enda fil i Blob Storage. Vi kallar loggfilen "badrows.csv".

    ![Dåliga rader](media/data-flow/error3.png)
    
4. Det slutförda dataflödet visas nedan. Vi kan nu dela upp felrader för att undvika SQL-trunkeringsfel och placera dessa poster i en loggfil. Under tiden kan framgångsrika rader fortsätta att skriva till vår måldatabas.

    ![fullständigt dataflöde](media/data-flow/error2.png)

## <a name="next-steps"></a>Nästa steg

* Skapa resten av dataflödeslogiken med hjälp av mappning av [dataflödensomvandlingar](concepts-data-flow-overview.md).
