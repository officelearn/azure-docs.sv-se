---
title: Använda Körningsvy i Data Lake Tools för Visual Studio
description: Den här artikeln beskriver hur du kan använda Körningsvy för examen Data Lake Analytics-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: jgao
ms.reviewer: jasonwhowell
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: 9f834d697c0d3fe537bbdb190b0ba0d0f294ac87
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041021"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Använda Körningsvy i Data Lake Tools för Visual Studio
Lär dig mer om att använda Körningsvy för examen Data Lake Analytics-jobb.


## <a name="open-the-vertex-execution-view"></a>Öppna Körningsvy
Öppna ett U-SQL-jobb i Data Lake Tools för Visual Studio. Klicka på **Körningsvy** i det nedre vänstra hörnet. Du kan uppmanas att först läsa in profiler och det kan ta lite tid beroende på din nätverksanslutning.

![Data Lake Analytics Tools Körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Förstå Körningsvy
Den Körningsvy består av tre delar:

![Data Lake Analytics Tools Körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Den **hörn väljare** på de vänstra kan du välja hörn genom funktioner (t.ex. de främsta 10 datanoder läsa, eller välj per steg). En av de mest använda filter är att se den **hörn på kritiska**. Den **kritiska vägen** är den längsta kedjan av formhörnen i ett U-SQL-jobb. Det är användbart för att optimera dina jobb genom att kontrollera vilken brytpunkt tar längst tid att förstå den kritiska vägen.
  
![Data Lake Analytics Tools Körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Överst i mitten fönstret visar den **Körningsstatus för alla hörn**.
  
![Data Lake Analytics Tools Körningsvy](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Center längst ned i fönstret visar information om varje brytpunkt:
* Processnamn: Namnet på instansen hörn. Den består av olika delar i StageName | VertexName | VertexRunInstance. Till exempel SV7_Split [62] .v1 hörn står för den andra instansen som körs (.v1, index som börjar från 0) för hörn tal 62 i steget SV7_Split.
* Totala Data Läs/skriftliga: Data har lästs/skrivits av det här hörn.
* Status/Avsluts-Status: Den slutgiltiga statusen när hörnet är avslutad.
* Avsluta kod/feltyp: Felet när hörnet misslyckades.
* Skapa en orsak: Varför hörnet skapades.
* Resursen svarstid/bearbeta svarstid/PN kö svarstid: den tid det tar för hörnet att vänta på resurser, att bearbeta data och för att stanna kvar i kön.
* Processen/skapare GUID: GUID för det aktuella körs hörnet eller dess skapare.
* Version: N: te-instansen körs hörnet (systemet kan schemalägga nya instanser av ett hörn för att få många orsaker, till exempel redundans, redundans, osv.)
* Version skapas en gång.
* Bearbeta skapa Start tid/bearbeta i kö tid/bearbeta Start tid/bearbeta slutförd tid: när vertex-processen börjar skapa; När hörn processen startar till kön; När vissa hörn processen startar; När vissa hörnet är slutförd.

## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill visa jobbinformation Se [Använd Jobbwebbläsare och Jobbvy för Azure Data lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md)
