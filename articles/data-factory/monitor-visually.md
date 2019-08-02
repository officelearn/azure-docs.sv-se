---
title: Övervaka Azure-datafabriker visuellt Microsoft Docs
description: Lär dig att visuellt övervaka Azures data fabriker
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: 53ead1caed47ae442670f0b6bcd54cd84956a759
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720606"
---
# <a name="visually-monitor-azure-data-factories"></a>Övervaka Azure-datafabriker visuellt
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda.

I den här snabb starten får du lära dig hur du övervakar Data Factory pipelines visuellt utan att skriva en enda rad kod.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="monitor-data-factory-pipelines"></a>Övervaka Data Factory pipelines

Övervaka pipelines och aktivitets körningar med ett enkelt gränssnitt för List visning. Alla körningar visas i den lokala webbläsarens tidszon. Du kan ändra tids zonen och alla datum-och tids fält fästs vid den valda tids zonen.  

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Logga in på [Azure Portal](https://portal.azure.com/).
3. Navigera till bladet skapad Data Factory i Azure Portal och klicka på panelen övervaka & hantering för att starta den Data Factory visuella övervaknings miljön.

## <a name="monitor-pipeline-runs"></a>Övervaka pipelinekörningar
Listvy för varje pipelinekörning i dina Data Factory v2-pipelines. Inkluderade kolumner:

| **Kolumn namn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namnet på pipeline. |
| Åtgärder | En enda åtgärd som är tillgänglig för att Visa aktivitets körningar. |
| Kör Start | Start datum tid för pipeline-körning (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Duration | Körnings tid (HH: MM: SS) |
| Aktiverad av | Manuell utlösare, schema utlösare |
| Status | Misslyckades, lyckades, pågår |
| Parametrar | Körnings parametrar för pipeline (namn, värdepar) |
| Fel | Fel i pipeline-körning (om/något) |
| Körnings-ID | ID för pipeline-körningen |

![Övervaka pipelinekörningar](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Övervaka aktivitetskörningar
Listvy med aktivitetskörningar som motsvarar respektive pipelinekörning. Klicka på ikonen **aktivitets körningar** under kolumnen **åtgärder** för att Visa aktivitets körningar för varje pipeline-körning. Inkluderade kolumner:

| **Kolumn namn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namnet på aktiviteten inuti pipelinen. |
| Aktivitetstyp | Typ av aktivitet, till exempel Copy, HDInsightSpark, HDInsightHive osv. |
| Kör Start | Start datum tid för aktivitets körning (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Duration | Körnings tid (HH: MM: SS) |
| Status | Misslyckades, lyckades, pågår |
| Indata | JSON-matris som beskriver aktivitetens indata |
| Output | JSON-matris som beskriver aktivitetens utdata |
| Fel | Aktivitets körnings fel (om/något) |

![Övervaka aktivitetskörningar](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Du måste klicka på **Uppdatera** -ikonen överst om du vill uppdatera listan över pipeline-och aktivitets körningar. Automatisk uppdatering stöds inte för tillfället.

![uppdatera](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Välj en data fabrik som ska övervakas
Hovra över **Data Factory** ikonen längst upp till vänster. Klicka på pilens ikon för att se en lista över Azure-prenumerationer och data fabriker som du kan övervaka.

![Välja datafabrik](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Konfigurera listvyn

### <a name="apply-rich-ordering-and-filtering"></a>Använd omfattande ordning och filtrering

Order pipelinen körs i DESC/ASC genom att köra start-och filter pipelines körs av följande kolumner:

| **Kolumn namn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namnet på pipeline. Alternativen inkluderar snabb filter för de senaste 24 timmarna, förra veckan, de senaste 30 dagarna eller Välj en anpassad datum tid. |
| Kör Start | Start datum och tid för pipeline-körning |
| Körnings status | Filter körningar efter status-lyckades, misslyckades, pågår |

![Filter](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Lägg till eller ta bort kolumner
Högerklicka på rubriken för listvyn och Välj kolumner som du vill ska visas i listvyn

![Kolumner](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Justera kolumn bredder
Öka och minska kolumn bredderna i list visningen genom att hovra över kolumn rubriken

## <a name="promote-user-properties-to-monitor"></a>Befordra användar egenskaper för övervakning

Du kan befordra valfri aktivitets egenskap för pipeline som en användar egenskap så att den blir en entitet som du kan övervaka. Du kan till exempel flytta **käll** -och **mål** egenskaperna för kopierings aktiviteten i din pipeline som användar egenskaper. Du kan också välja **generera automatiskt** för att generera egenskaperna för **käll** -och **mål** användare för en kopierings aktivitet.

![Skapa användar egenskaper](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Du kan bara flytta upp till 5 pipeline-aktivitets egenskaper som användar egenskaper.

När du har skapat användar egenskaperna kan du övervaka dem i vyerna i övervaknings listan. Om källan för kopierings aktiviteten är ett tabell namn kan du övervaka käll tabellens namn som en kolumn i list visningen aktivitets körningar.

![Aktivitets körnings listan utan användar egenskaper](media/monitor-visually/monitor-user-properties-image2.png)

![Lägg till kolumner för användar egenskaper till aktivitets körnings listan](media/monitor-visually/monitor-user-properties-image3.png)

![Aktivitets körnings lista med kolumner för användar egenskaper](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Kör om aktiviteter i en pipeline

Nu kan du köra om aktiviteter i en pipeline. Klicka på **Visa aktivitets körningar** och välj aktiviteten i din pipeline från vilken plats du vill köra pipelinen igen.

![Visa aktivitetskörningar](media/monitor-visually/rerun-activities-image1.png)

![Välj en aktivitets körning](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Visa körnings historik igen

Du kan visa körnings historiken för alla pipelines i list visningen.

![Visa historik](media/monitor-visually/rerun-history-image1.png)

Du kan också Visa körnings historiken för en viss pipeline-körning.

![Visa historik för en pipeline-körning](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-vyer

Använd Gantt-vyer för att snabbt visualisera dina pipeliner och aktivitets körningar. Du kan titta på vyn Gantt-visning per pipeline eller grupp efter anteckningar/taggar som du har skapat i pipelinen.

![Gantt-diagram](media/monitor-visually/gantt1.png)

![Anteckningar i Gantt-diagram](media/monitor-visually/gantt2.png)

Längden på fältet informerar pipelinens varaktighet. Du kan också klicka på fältet för att se mer information.

![Varaktighet för Gantt-schema](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Guidade visningar
Klicka på informations ikonen i det nedre vänstra hörnet och klicka på guidade visningar för att få stegvisa instruktioner om hur du övervakar pipeline och aktivitets körningar.

![Guidade visningar](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Feedback
Klicka på feedback-ikonen för att ge oss feedback om de olika funktionerna eller eventuella problem som du kan behöva.

![Feedback](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Aviseringar

Du kan generera aviseringar för mått som stöds i Data Factory. Välj **övervakare > varningar & mått** på Data Factory Monitor-sidan för att komma igång.

![](media/monitor-visually/alerts01.png)

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Skapa aviseringar

1.  Klicka på **ny aviserings regel** för att skapa en ny avisering.

    ![](media/monitor-visually/alerts02.png)

1.  Ange regel namnet och välj **allvarlighets grad**för avisering.

    ![](media/monitor-visually/alerts03.png)

1.  Välj aviserings villkoret.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Konfigurera aviserings logiken. Du kan skapa en avisering för det valda måttet för alla pipeliner och motsvarande aktiviteter. Du kan också välja en viss aktivitets typ, ett aktivitets namn, ett pipelin namn eller en typ av problem.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurera **e-post/SMS/push/Voice** -meddelanden för aviseringen. Skapa eller Välj en befintlig **Åtgärds grupp** för aviserings meddelanden.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Skapa varnings regeln.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Nästa steg

Se [övervaka och hantera pipelines program mässig](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) artikel för att lära dig mer om övervakning och hantering av pipeliner.
