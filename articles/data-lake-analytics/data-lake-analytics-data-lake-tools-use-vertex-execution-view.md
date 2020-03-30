---
title: Vertex execution view i DataSjöverktyg för Visual Studio
description: I den här artikeln beskrivs hur du använder Vertex Execution View för att utföra datasjöanalysjobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309716"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Använda vertexutförandevyn i datasjöverktyg för Visual Studio
Lär dig hur du använder Vertex Execution View för att utföra datasjöanalysjobb.


## <a name="open-the-vertex-execution-view"></a>Öppna körningsvyn för Vertex
Öppna ett U-SQL-jobb i DataSjöverktyg för Visual Studio. Klicka på **Körningsvy** för Vertex i det nedre vänstra hörnet. Du kan uppmanas att läsa in profiler först och det kan ta lite tid beroende på nätverksanslutningen.

![DataSjöanalysverktyg Vertex-körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Förstå körningsvyn för Vertex
Vertex Execution View har tre delar:

![DataSjöanalysverktyg Vertex-körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

**Med Vertex-väljaren** till vänster kan du välja hörn efter funktioner (till exempel topp 10 data som läses eller välja efter steg). Ett av de vanligaste filtren är att se **hörnen på den kritiska linjen**. Den **kritiska linjen** är den längsta kedjan av hörn för ett U-SQL-jobb. Att förstå den kritiska linjen är användbart för att optimera dina jobb genom att kontrollera vilket hörn som tar längst tid.
  
![DataSjöanalysverktyg Vertex-körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Det övre mittenfönstret visar **körstatus för alla hörn**.
  
![DataSjöanalysverktyg Vertex-körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Det nedre mittenfönstret visar information om varje hörn:
* Processnamn: Namnet på hörninstansen. Den består av olika delar i StageName| VertexName| VertexRunInstance. Till exempel står SV7_Split[62].v1-vertex för den andra löpinstansen (.v1, index från 0) av Vertex nummer 62 i steg SV7_Split.
* Totalt antal lästa/skrivna data: Data lästes/skrevs av detta hörn.
* Tillstånds-/avslutningsstatus: Den slutliga statusen när hörnet avslutas.
* Typ av avslutningskod/fel: Felet när hörnet misslyckades.
* Skapa orsak: Varför hörnet skapades.
* Resurssvarstid/Process svarstid/PN kö svarstid: den tid det tar för hörnet att vänta på resurser, att bearbeta data och att stanna i kön.
* Process/Creator GUID: GUID för det aktuella hörnet som körs eller dess skapare.
* Version: N-th-instansen av det löphörn (systemet kan schemalägga nya instanser av ett hörn av många skäl, till exempel redundans, beräkningsredundans osv.)
* Version Skapad tid.
* Process Skapa starttid/process köad tid/process starttid/process slutförd tid: när vertexprocessen börjar skapas; när vertexprocessen börjar köa. när den vissa vertexprocessen startar. när vissa hörn är klar.

## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om hur du visar jobbinformation finns i [Använda jobbwebbläsare och jobbvy för Azure Data lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md)
