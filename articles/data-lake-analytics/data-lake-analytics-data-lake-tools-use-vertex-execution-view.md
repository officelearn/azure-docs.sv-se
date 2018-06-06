---
title: Använd vyn Vertex körning i Data Lake-verktyg för Visual Studio
description: Den här artikeln beskriver hur du använder vyn Vertex körningen till examen Data Lake Analytics-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: jgao
manager: kfile
editor: jasonwhowell
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: af15bb9fd1131f598dc87f13c4af481b63d023e3
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735449"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Använd vyn Vertex körning i Data Lake-verktyg för Visual Studio
Lär dig använda Vertex körning vyn examen Data Lake Analytics-jobb.


## <a name="open-the-vertex-execution-view"></a>Öppna vyn Vertex körning
Öppna ett U-SQL-jobb i Data Lake-verktyg för Visual Studio. Klicka på **Vertex vy** i det nedre vänstra hörnet. Du kan uppmanas att först läsa in profiler och det kan ta lite tid beroende på nätverksanslutningen.

![Data Lake Analytics verktyg Vertex körning visa](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Förstå Vertex vy
Vyn Vertex körningen har tre delar:

![Data Lake Analytics verktyg Vertex körning visa](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Den **Vertex selector** på vänster kan du välja formhörnen av funktioner (som topp 10 data läsa eller välja efter steget). En av de mest använda filter är att se den **formhörnen på kritiska**. Den **kritiska** är den längsta kedjan av formhörnen ett U-SQL-jobb. Förstå viktiga sökvägen är användbart för att optimera dina jobb genom att kontrollera vilka vertex tar längst tid.
  
![Data Lake Analytics verktyg Vertex körning visa](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Fönstret visas i mitten av **Körningsstatus för alla formhörnen**.
  
![Data Lake Analytics verktyg Vertex körning visa](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Längst ned i fönstret center visar information om varje vertex:
* Processnamnet: Namnet på vertex-instans. Den består av olika delar i StageName | VertexName | VertexRunInstance. Till exempel SV7_Split [62] .v1 vertex står för den andra körs instansen (.v1, index som börjar från 0) för Vertex tal 62 i steget SV7_Split.
* Totala Data Läs/skriftliga: Data har lästs/skrivits av den här hörn.
* Tillstånd-/ utgång Status: Slutlig status när vertex avslutas.
* Avsluta felkod-fel typ: Felet när vertex misslyckades.
* Skapa orsak: Varför vertex skapades.
* Resursen latens/bearbeta latens/PN kön svarstid: tiden för vertex vänta resurser för att bearbeta data och för att stanna kvar i kön.
* Processen/Creator GUID: GUID för den aktuella brytpunkten som körs eller dess creator.
* Version: N: e-instansen körs vertex (systemet kan schemalägga nya instanser för ett hörn för många orsaker, till exempel redundans compute redundans, osv.)
* Version skapas en gång.
* Bearbeta skapa Start tid/Process i kö tid/Process Start tid/Process slutförd tid: när vertex-processen börjar skapa; När processen vertex startar till kön; När vissa vertex processen startas; När vissa vertex har slutförts.

## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill visa jobbinformation [Använd jobbet webbläsare och visa jobb för Azure Data lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md)
