---
title: Mappa visuell övervakning av dataflöde
description: Så här övervakar du Azure Data Flows för visuellt
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: 93d92286fa9eecbc64229059274cc8f9ed99e21e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928282"
---
# <a name="monitor-data-flows"></a>Övervaka dataflöden



När du har slutfört uppbyggnaden och felsökningen av dataflödet vill du schemalägga dataflödet så att det körs enligt ett schema inom ramen för en pipeline. Du kan schemalägga pipelinen från Azure Data Factory med utlösare. Eller så kan du använda alternativet Utlösare nu från Azure Data Factory Pipeline Builder för att köra en körning med en körning för att testa dataflödet i pipelinekontexten.

När du kör pipelinen kan du övervaka pipelinen och alla aktiviteter som finns i pipelinen, inklusive aktiviteten Dataflöde. Klicka på bildskärmsikonen på den vänstra panelen Azure Data Factory UI. Du kommer att se en skärm som liknar den nedan. De markerade ikonerna gör att du kan öka detaljnivån i aktiviteterna på gång, inklusive aktiviteten Dataflöde.

![Dataflödesövervakning](media/data-flow/mon001.png "Dataflödesövervakning")

Du kommer att se statistik på denna nivå samt inklusive körtider och status. Kör-ID:et på aktivitetsnivå är annorlunda än körnings-ID:et på pipeline-nivå. Kör-ID:et på föregående nivå är för pipelinen. Genom att klicka på glasögonen får du djup information om hur du ska utföra dataflödet.

![Dataflödesövervakning](media/data-flow/mon002.png "Dataflödesövervakning")

När du befinner dig i den grafiska nodövervakningsvyn visas en förenklad version av dataflödesdiagrammet.

![Dataflödesövervakning](media/data-flow/mon003.png "Dataflödesövervakning")

## <a name="view-data-flow-execution-plans"></a>Visa planer för körning av dataflöde

När ditt dataflöde körs i Spark bestämmer Azure Data Factory optimala kodsökvägar baserat på hela dataflödet. Dessutom kan körningssökvägarna uppstå på olika utskalningsnoder och datapartitioner. Därför representerar övervakningsdiagrammet utformningen av ditt flöde, med hänsyn till körningsbanan för dina omvandlingar. När du klickar på enskilda noder visas "grupperingar" som representerar kod som kördes tillsammans i klustret. De tidpunkter och antal som du ser representerar dessa grupper i motsats till de enskilda stegen i din design.

![Dataflödesövervakning](media/data-flow/mon004.png "Dataflödesövervakning")

* När du klickar på det öppna utrymmet i övervakningsfönstret visas tids- och radantal för varje diskho och de omvandlingar som ledde till sink-data för omvandlingslinje.

* När du väljer enskilda omvandlingar får du ytterligare feedback på den högra panelen som visar partitionsstatistik, kolumnantal, skevhet (hur jämnt är data fördelade över partitioner) och kurtosis (hur taggiga är data).

* När du klickar på handfatet i nodvyn visas kolumn härstamning. Det finns tre olika metoder som kolumner ackumuleras i hela dataflödet för att landa i diskbänken. De är:

  * Beräknad: Du använder kolumnen för villkorlig bearbetning eller inom ett uttryck i dataflödet, men inte landar den i sink
  * Härledd: Kolumnen är en ny kolumn som du genererade i flödet, dvs den fanns inte i källan
  * Mappad: Kolumnen kommer från källan och du mappar den till ett sink-fält
  * Dataflödesstatus: Den aktuella statusen för körningen
  * Starttid för kluster: Hur lång tid det går att hämta beräkningsmiljön JIT Spark för körning av dataflöde
  * Antal transformeringar: Hur många omvandlingssteg som körs i flödet
  
![Dataflödesövervakning](media/data-flow/monitornew.png "Dataflödesövervakning Ny")  
  
## <a name="monitor-icons"></a>Övervaka ikoner

Den här ikonen innebär att omvandlingsdata redan har cachelagrats i klustret, så tidsinställningarna och körningssökvägen har tagit hänsyn till detta:

![Dataflödesövervakning](media/data-flow/mon004.png "Dataflödesövervakning")

Du kommer också att se ikoner för grön cirkel i omvandlingen. De representerar ett antal sänkor som data flödar in i.
