---
title: Azure Data Factory mappning Data Flow felsökningsläge
description: Starta en interaktiv debug session när du skapar data flödar
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147362"
---
# <a name="mapping-data-flow-debug-mode"></a>Felsökningsläge för mappning av Data flöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mappning dataflöde felsökningsläge kan vara påslaget med knappen ”Data flöda felsöka” högst upp på designytan. När designa dataflöden, aktivera felsökningsläget gör att du kan se hur data interaktivt forma transformeringen när du skapar och felsöker dina dataflöden. Felsökningssessionen kan användas både i sessioner med arkitekturdesign för dataflöde samt vid felsökning av pipelinekörning av data.

![Felsöka knappen](media/data-flow/debugbutton.png "Debug-knappen")

## <a name="overview"></a>Översikt
När felsökningsläget är aktiverat, ska du interaktivt skapa ditt dataflöde med en aktiv Spark-kluster. Sessionen stängs inaktiveras felsökning i Azure Data Factory. Du bör känna till de debitering åsamkar Azure Databricks under den tid som du har aktiverat felsökningssessionen.

I de flesta fall är det en bra idé att skapa din Data som flödar i felsökningsläge så att du kan verifiera din affärslogik och visa dina dataomvandlingar innan du publicerar ditt arbete i Azure Data Factory. Använd knappen ”felsökning” på panelen pipeline för att testa ditt dataflöde i en pipeline.

> [!NOTE]
> Debug läge ljus är grön i verktygsfältet Data Factory, debiteras du priset dataflöde felsökning av 8 kärnor/timme för allmän beräkning med en 60 minuters time to live 

> [!NOTE]
>När du kör i felsökningsläge i dataflöde kan dina data skrivs inte till mottagaren omvandla. En felsökningssession är avsedd att fungera som en testmiljö för dina transformeringar. Mottagare krävs inte vid felsökning och ignoreras i ditt dataflöde. Om du vill testa Skrivningen av data i dina mottagare kör Data flöda från en Azure Data Factory-Pipeline och använder Debug-körning från en pipeline.

## <a name="debug-settings"></a>Inställningar för felsökning
Felsöka kan redigera inställningarna genom att klicka på ”felsöka inställningar” i verktygsfältet dataflöde arbetsytan. Du kan välja de begränsningar och/eller filkälla ska användas för var och en av dina käll-transformeringar här. Radbegränsningar i den här inställningen är endast för den aktuella felsökningssessionen. Du kan också välja den mellanlagringslänkade tjänsten som ska användas för en SQL DW-källa. 

![Inställningar för felsökning](media/data-flow/debug-settings.png "inställningar för felsökning")

## <a name="cluster-status"></a>Klusterstatus
Det finns en kluster-statusindikator överst på designytan som blir grön när klustret är klart för felsökning. Om klustret redan varma visas grön indikator nästan omedelbart. Om klustret inte redan körs när du har angett felsökningsläge och har du vänta 5 – 7 minuter att skapa klustret. Indikatorn ska starta fram till dess redo.

När du är klar med din felsökning, inaktivera växeln felsökning så att avsluta ditt Azure Databricks-kluster och du kommer inte längre att debiteras för debug-aktivitet.

## <a name="data-preview"></a>Dataförhandsgranskning
Felsökning på fliken förhandsgranskningen kommer ljus upp i den nedre rutan. Utan felsökningsläget vid visar dataflöde endast den aktuella metadata och från var och en av dina transformeringar på fliken Granska. Förhandsgranskning frågar endast antalet rader som du har angett som din gräns i inställningar för felsökning. Du kan behöva klicka på ”Hämta data” Uppdatera förhandsgranskning.

![Dataförhandsgranskning](media/data-flow/datapreview.png "förhandsgranskning")

## <a name="data-profiles"></a>Data-profiler
Att välja enskilda kolumner i din preview data-fliken visas ett diagram längst till höger din datarutnätet med detaljerad statistik om varje fält. Azure Data Factory blir en bestämning baserat på datasampling vilken typ av diagrammet ska visas. Hög kardinalitet fält som standard /inte NULL diagram medan kategoriska och numeriska data som har låg kardinalitet visar stapeldiagram som visar data värdet frekvens. Max/len lång strängfält, min/max-värden i numeriska fält, standard utveckling, percentiler, antal och genomsnittlig visas också. 

![Kolumnstatistik](media/data-flow/stats.png "kolumnstatistik")

## <a name="next-steps"></a>Nästa steg

När du är klar utvecklar och felsöker ditt dataflöde [köra den från en pipeline.](control-flow-execute-data-flow-activity.md)

När du testar en pipeline med ett dataflöde kan du använda pipelinen [Debug kör körning alternativet.](iterative-development-debugging.md)
