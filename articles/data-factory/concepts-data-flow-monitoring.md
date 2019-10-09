---
title: Azure Data Factory mappning av visuell övervakning av data flöde
description: Övervaka Azure Data Factory data flöden visuellt
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 41b4b7cd911bad40055fcf527c186f8de9466cdc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030206"
---
# <a name="monitor-data-flows"></a>Övervaka data flöden



När du har skapat och felsöker ditt data flöde vill du schemalägga ditt data flöde så att det körs enligt ett schema inom ramen för en pipeline. Du kan schemalägga pipelinen från Azure Data Factory med utlösare. Eller så kan du använda alternativet utlösare nu från Azure Data Factory pipeline Builder för att köra en körning med en körning för att testa ditt data flöde inom pipeline-kontexten.

När du kör din pipeline kan du övervaka pipelinen och alla aktiviteter som finns i pipelinen, inklusive data flödes aktiviteten. Klicka på ikonen övervaka i panelen till vänster Azure Data Factory användar gränssnitt. Du ser en skärm som liknar den som visas nedan. Med de markerade ikonerna kan du öka detalj nivån i aktiviteterna i pipelinen, inklusive data flödes aktiviteten.

Övervakning av data flöde för ![övervaknings](media/data-flow/mon001.png "data") flöde

Statistik visas på den här nivån, inklusive körnings tider och-status. Körnings-ID: t på aktivitets nivån skiljer sig från körnings-ID: t på pipeline-nivån. Körnings-ID: t på den föregående nivån är för pipelinen. Genom att klicka på glasögon får du djupgående information om din data flödes körning.

Övervakning av data flöde för ![övervaknings](media/data-flow/mon002.png "data") flöde

När du är i vyn övervakning av grafisk nod visas en förenklad visnings version av data flödes diagrammet.

Övervakning av data flöde för ![övervaknings](media/data-flow/mon003.png "data") flöde

## <a name="view-data-flow-execution-plans"></a>Visa körnings planer för data flöde

När ditt data flöde körs i Spark, fastställer Azure Data Factory optimala kod Sök vägar baserat på hela ditt data flöde. Dessutom kan sökvägar för körning ske på olika skalbara noder och datapartitioner. Därför representerar övervaknings diagrammet utformningen av ditt flöde, med hänsyn till körnings vägen för dina transformeringar. När du klickar på enskilda noder visas "grupperingar" som representerar kod som kördes tillsammans i klustret. De tids inställningar och räknare som du ser representerar dessa grupper i stället för de enskilda stegen i din design.

Övervakning av data flöde för ![övervaknings](media/data-flow/mon004.png "data") flöde

* När du klickar på det öppna utrymmet i övervaknings fönstret, visar statistik i den nedre rutan tid och antal rader för varje mottagare och de omvandlingar som ledde till Sink-data för omvandlings härkomst.

* När du väljer enskilda transformeringar får du ytterligare feedback på den högra panelen som visar partitionerings statistik, kolumn antal, snedhet (hur jämnt är data som distribueras mellan partitioner) och toppar (hur höga data är).

* När du klickar på mottagaren i vyn Node visas kolumnen härkomst. Det finns tre olika metoder som kolumner samlas in i data flödet för att hamna i mottagaren. De är:

  * Beräknade Du kan använda kolumnen för villkorlig bearbetning eller inom ett uttryck i ditt data flöde, men inte landa den i mottagaren
  * Följer Kolumnen är en ny kolumn som du har genererat i ditt flöde, dvs. den fanns inte i källan
  * Mappar Kolumnen som kommer från källan och din mappas till fältet mottagare
  
## <a name="monitor-icons"></a>Övervaka ikoner

Den här ikonen innebär att Transformations data redan har cachelagrats i klustret, så tids inställningarna och körnings Sök vägen har tagit detta i beräkningen:

Övervakning av data flöde för ![övervaknings](media/data-flow/mon004.png "data") flöde

Du ser även gröna cirkel ikoner i omvandlingen. De representerar antalet handfat som data flödar in i.
