---
title: Azure Data Factory mappning Data Flow felsökningsläge
description: Starta en interaktiv debug session när du skapar data flödar
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 82559c89094649b377dc2a898798ce6aad5fbcc7
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570495"
---
# <a name="mapping-data-flow-debug-mode"></a>Felsökningsläge för mappning av Data flöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mappning dataflöde har felsökningsläge och som kan tändas med Debug-knappen högst upp på designytan. När designa dataflöden, ställa in felsökningsläge på kan du interaktivt se hur data forma transformeringen när du skapar och felsöker dina dataflöden.

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>Översikt
När felsökningsläge finns på, skapar du interaktivt ditt dataflöde med ett aktivt interaktiva Azure Databricks-kluster. Sessionen stängs inaktiveras felsökning i Azure Data Factory. Du bör känna till de debitering åsamkar Azure Databricks under den tid som du har aktiverat felsökningssessionen.

I de flesta fall är det en bra idé att skapa din Data som flödar i felsökningsläge så att du kan verifiera din affärslogik och visa dina dataomvandlingar innan du publicerar ditt arbete i Azure Data Factory.

## <a name="debug-mode-on"></a>Felsökningsläge på
När du växlar på felsökningsläge uppmanas du att ett sidpanel formulär som du blir ombedd att peka på dina interaktiva Azure Databricks-kluster och välja alternativ för provtagning källa. Du måste använda en interaktiv kluster från Azure Databricks och välj antingen en sampling storlek från varje källa-transformeringar eller välj en textfil för testdata.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>När du kör i felsökningsläge i dataflöde kan dina data skrivs inte till mottagaren omvandla. En felsökningssession är avsedd att fungera som ett test > stomme för dina transformeringar. Mottagare krävs inte vid felsökning och ignoreras i ditt dataflöde. Om du vill testa att data > i din mottagare kör Data flöda från en Azure Data Factory-Pipeline och använder Debug-körning från en pipeline.

## <a name="debug-settings"></a>Inställningar för felsökning
Inställningar för felsökning kan vara varje källa från ditt dataflöde visas i panelen på klientsidan och kan även redigeras genom att välja ”datakällans inställningar” dataflöde designerverktygsfältet. Du kan välja de begränsningar och/eller källa för att använda den här källan omvandlingen för var och en. Radbegränsningar i den här inställningen är endast för den aktuella felsökningssessionen. Du kan också använda inställningen Sampling i källan för att begränsa rader i källan transforamtion.

## <a name="cluster-status"></a>Klusterstatus
Det finns en kluster-statusindikator överst på designytan som blir grön när klustret är klart för felsökning. Om klustret redan varma visas grön indikator nästan omedelbart. Om klustret inte körs redan när du har angett felsökningsläge, och du måste vänta 5 – 7 minuter att skapa klustret. Indikatorljus blir gul tills den är klar. När klustret är redo för dataflöde debug blir indikatorljus grön.

När du är klar med din felsökning, tur felsöka inaktivera så att avsluta ditt Azure Databricks-kluster.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Dataförhandsgranskning
Felsökning på fliken förhandsgranskningen kommer ljus upp i den nedre rutan. Utan felsökningsläget vid visar dataflöde endast den aktuella metadata och från var och en av dina transformeringar på fliken Granska. Förhandsgranskning frågar endast antalet rader som du har angett som din gräns i inställningar för datakälla. Du kan behöva klicka på ”Hämta data” Uppdatera förhandsgranskning.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Data-profiler
Genom att markera enskilda kolumner i dina data-fliken för förhandsversionen kommer popup-fönster och ett diagram längst till höger din datarutnätet med detaljerad statistik om varje fält. Azure Data Factory blir en bestämning baserat på datasampling vilken typ av diagrammet ska visas. Hög kardinalitet fält som standard när NULL / NOT NULL diagram medan kategoriska och numeriska data som har låg kardinalitet visar stapeldiagram som visar data värdet frekvens. Du kan även se max / len längden på strängfält, min / max värdena i numeriska fält, standard utveckling, percentiler, antal och genomsnittlig. 

<img src="media/data-flow/chart.png" width="400">
