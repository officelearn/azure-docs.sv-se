---
title: Felsökningsläge för dataflöde
description: Starta en interaktiv felsökningssession när du skapar dataflöden
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 4a763a3bb4d46ba03808423d4d1283381c1174a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605395"
---
# <a name="mapping-data-flow-debug-mode"></a>Felsökningsläge för dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Översikt

Azure Data Factory-mappningsdataflödets felsökningsläge gör att du interaktivt kan titta på dataformstransformeringen medan du skapar och felsöker dina dataflöden. Felsökningssessionen kan användas både i designsessioner för dataflöde och vid pipeline-felsökningskörning av dataflöden. För att aktivera felsökningsläge, använd knappen "DataFlödesfelsökning" högst upp på designytan.

![Skjutreglage för felsökning](media/data-flow/debugbutton.png "Skjutreglage för felsökning")

När du har aktiverat skjutreglaget uppmanas du att välja vilken konfiguration för integreringskörning du vill använda. Om AutoResolveIntegrationRuntime väljs, kommer ett kluster med åtta kärnor av allmän beräkning med 60 minuters tid att leva att snurras upp. Mer information om körning för integrering av dataflöde finns i [Dataflödesprestanda](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![FelsökA IR-val](media/data-flow/debugbutton2.png "FelsökA IR-val")

När felsökningsläget är aktiverat skapar du interaktivt ditt dataflöde med ett aktivt Spark-kluster. Sessionen stängs när du inaktiverar felsökning i Azure Data Factory. Du bör vara medveten om de timavgifter som Azure Databricks ådrar sig under den tid som du har aktiverat felsökningssessionen.

I de flesta fall är det en bra idé att skapa dina dataflöden i felsökningsläge så att du kan validera din affärslogik och visa dina dataomvandlingar innan du publicerar ditt arbete i Azure Data Factory. Använd knappen "Debug" på pipelinepanelen för att testa dataflödet i en pipeline.

## <a name="cluster-status"></a>Klusterstatus

Klusterstatusindikatorn högst upp på designytan blir grön när klustret är redo för felsökning. Om klustret redan är varmt visas den gröna indikatorn nästan omedelbart. Om klustret inte redan kördes när du gick in i felsökningsläge måste du vänta 5-7 minuter innan klustret ska snurra. Indikatorn snurrar tills den är klar.

När du är klar med felsökningen inaktiverar du felsökningsknappen så att Azure Databricks-klustret kan avslutas och du debiteras inte längre för felsökningsaktivitet.

## <a name="debug-settings"></a>Felsökningsinställningar

Felsökningsinställningar kan redigeras genom att klicka på "Felsökningsinställningar" i verktygsfältet Dataflödesarbetsyt. Du kan välja den radgräns eller filkälla som ska användas för var och en av källomformningerna här. Radgränserna i den här inställningen är endast för den aktuella felsökningssessionen. Du kan också välja den mellanlagringslänkade tjänst som ska användas för en SQL DW-källa. 

![Felsökningsinställningar](media/data-flow/debug-settings.png "Felsökningsinställningar")

Om du har parametrar i dataflödet eller någon av dess refererade datauppsättningar kan du ange vilka värden som ska användas under felsökning genom att välja fliken **Parametrar.**

![Parametrar för felsökningsinställningar](media/data-flow/debug-settings2.png "Parametrar för felsökningsinställningar")

## <a name="data-preview"></a>Förhandsgranskning

När felsökning är aktiverat tänds fliken Förhandsgranskning av data på den nedre panelen. Utan felsökningsläge på visar Dataflöde bara de aktuella metadata i och från var och en av dina omvandlingar på fliken Inspektera. Förhandsgranskningen av data frågar bara antalet rader som du har angett som din gräns i felsökningsinställningarna. Klicka på **Uppdatera** om du vill hämta förhandsgranskningen av data.

![Förhandsgranskning](media/data-flow/datapreview.png "Förhandsgranskning")

> [!NOTE]
> Filkällor begränsar bara de rader som visas, inte de rader som läss. För mycket stora datauppsättningar rekommenderar vi att du tar en liten del av filen och använder den för testningen. Du kan välja en temporär fil i Felsökningsinställningar för varje källa som är en fildatauppsättningstyp.

När du kör i felsökningsläge i dataflöde skrivs inte dina data till sink-transformeringen. En felsökningssession är avsedd att fungera som en testsele för dina omvandlingar. Diskhoar krävs inte under felsökning och ignoreras i ditt dataflöde. Om du vill testa att skriva data i din sink kör du dataflödet från en Azure Data Factory Pipeline och använder felsökningskörningen från en pipeline.

### <a name="testing-join-conditions"></a>Testa anslutningsvillkor

När enhetstestar Kopplingar, Finns eller Uppslagsomvandlingar kontrollerar du att du använder en liten uppsättning kända data för testet. Du kan använda alternativet Felsökningsinställningar ovan för att ange en temporär fil som ska användas för testningen. Detta behövs eftersom när du begränsar eller sampling rader från en stor datauppsättning, kan du inte förutsäga vilka rader och vilka nycklar som kommer att läsas in i flödet för testning. Resultatet är icke-deterministiskt, vilket innebär att dina kopplingsvillkor kan misslyckas.

### <a name="quick-actions"></a>Snabba åtgärder

När du ser förhandsgranskningen av data kan du generera en snabb omformning för att typecast, ta bort eller göra en ändring i en kolumn. Klicka på kolumnrubriken och välj sedan ett av alternativen i verktygsfältet för förhandsgranskning av data.

![Snabba åtgärder](media/data-flow/quick-actions1.png "Snabba åtgärder")

När du har valt en ändring uppdateras förhandsgranskningen av data omedelbart. Klicka på **Bekräfta** i det övre högra hörnet för att generera en ny omvandling.

![Snabba åtgärder](media/data-flow/quick-actions2.png "Snabba åtgärder")

**Typecast** och **Modify** genererar en härledd kolumnomvandling och **ta bort** genererar en Select-omvandling.

![Snabba åtgärder](media/data-flow/quick-actions3.png "Snabba åtgärder")

> [!NOTE]
> Om du redigerar dataflödet måste du hämta förhandsgranskningen av data igen innan du lägger till en snabb omformning.

### <a name="data-profiling"></a>Dataprofilering

Om du markerar en kolumn på fliken Förhandsgranskning av data och om du klickar på Statistik i **verktygsfältet** för förhandsgranskning av data visas ett diagram längst till höger i datarutnätet med detaljerad statistik om varje fält. Azure Data Factory gör en bedömning baserat på dataprovtagningen för vilken typ av diagram som ska visas. Fält med hög kardinalitet kommer som standard null/NOT NULL-diagram att vara standard, medan kategoriska och numeriska data med låg kardinalitet visar stapeldiagram som visar datavärdefrekvens. Du ser också max/längd på strängfält, min/max-värden i numeriska fält, standardde dev, percentiler, antal och medelvärde.

![Kolumnstatistik](media/data-flow/stats.png "Kolumnstatistik")

## <a name="next-steps"></a>Nästa steg

* När du är klar med att skapa och felsöka dataflödet [kör du det från en pipeline.](control-flow-execute-data-flow-activity.md)
* När du testar pipelinen med ett dataflöde använder du [körningsalternativet för pipeline-felsökningskörning.](iterative-development-debugging.md)
