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
ms.openlocfilehash: e680610263830c856a3ff902d8e55cd8b2b4a158
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114571"
---
# <a name="visually-monitor-azure-data-factories"></a>Övervaka Azure-datafabriker visuellt
Azure Data Factory är en molnbaserad data integrerings tjänst. Du kan använda den för att skapa data drivna arbets flöden i molnet för att dirigera och automatisera data förflyttning och data omvandling. Med hjälp av Azure Data Factory kan du:

- Skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan mata in data från olika datalager.
- Bearbeta/transformera data med hjälp av beräknings tjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning.
- Publicera utdata till datalager som Azure SQL Data Warehouse och som sedan kan användas av Business Intelligence-program (BI).

I den här snabb starten får du lära dig hur du övervakar Data Factory pipelines visuellt utan att skriva en enda rad kod.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="monitor-data-factory-pipelines"></a>Övervaka Data Factory pipelines

Övervaka pipeline och aktivitet körs med ett enkelt gränssnitt för List visning. Alla körningar visas i webbläsarens lokala tidszon. Om du ändrar tids zonen fästs alla datum/tid-fält på den som du har valt.  

1. Starta Microsoft Edge eller Google Chrome. För närvarande stöds Data Factory-gränssnittet bara i dessa två webbläsare.
2. Logga in på [Azure Portal](https://portal.azure.com/).
3. Gå till bladet för den skapade data fabriken i Azure Portal. Välj panelen **övervaka & hantera** för att starta den Data Factory visuella övervaknings miljön.

## <a name="monitor-pipeline-runs"></a>Övervaka pipelinekörningar
I listvyn visas varje pipeline-körning för Data Factory pipeliner. Den innehåller följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namn på pipelinen |
| Åtgärder | Enskild åtgärd som är tillgänglig för att Visa aktivitets körningar |
| Kör Start | Start datum och-tid för pipeline-körningen (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Duration | Körnings tid (HH: MM: SS) |
| Aktiverad av | Manuell utlösare eller schemalagd utlösare |
| State | **Misslyckades**, **lyckades**eller **pågår** |
| Parametrar | Parametrar för pipeline-körningen (namn/värde-par) |
| Fel | Fel i pipeline-körning (om det finns) |
| Körnings-ID | ID för pipeline-körningen |

![Listvy för övervakning av pipeline-körningar](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Övervaka aktivitetskörningar
Listvyn visar aktivitets körningar som motsvarar varje pipeline-körning. Om du vill visa aktivitets körningar för varje pipeline-körning väljer du ikonen **aktivitet körs** under kolumnen **åtgärder** . Listvyn innehåller följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namn på aktiviteten inuti pipelinen |
| Aktivitetstyp | Typ av aktivitet, till exempel **copy**, **HDInsightSpark**eller **HDInsightHive** |
| Kör Start | Start datum och-tid för aktivitets körningen (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Duration | Körnings tid (HH: MM: SS) |
| State | **Misslyckades**, **lyckades**eller **pågår** |
| Indata | JSON-matris som beskriver aktivitetens indata |
| Output | JSON-matris som beskriver aktivitetens utdata |
| Fel | Aktivitets körnings fel (om det finns) |

![Listvy för övervakning av aktivitets körningar](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Du måste välja knappen **Uppdatera** överst för att uppdatera listan över pipeline-och aktivitets körningar. Automatisk uppdatering stöds inte för tillfället.

![Knappen Uppdatera](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Välj en data fabrik som ska övervakas
Hovra över **Data Factory** ikonen längst upp till vänster. Välj pilen för att se en lista med Azure-prenumerationer och data fabriker som du kan övervaka.

![Välj data fabriken](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Konfigurera listvyn

### <a name="apply-rich-ordering-and-filtering"></a>Använd omfattande ordning och filtrering

Order pipelinen körs i DESC/ASC enligt kör Start tiden. Filtrera Pipeline-körningar med hjälp av följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namnet på pipeline. Alternativen inkluderar snabb filter för de **senaste 24 timmarna**, **senaste veckan**och de **senaste 30 dagarna**. Eller Välj ett anpassat datum och tid. |
| Kör Start | Start datum och-tid för pipeline-körningen. |
| Körnings status | Filter körs efter status: **Lyckades**, **misslyckades**eller **pågår**. |

![Alternativ för filtrering](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Lägg till eller ta bort kolumner
Högerklicka på rubriken för listvyn och Välj kolumner som du vill ska visas i listvyn.

![Alternativ för kolumner](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Justera kolumn bredder
Öka och minska kolumn bredderna i listvyn genom att hovra över kolumn rubriken.

## <a name="promote-user-properties-to-monitor"></a>Befordra användar egenskaper för övervakning

Du kan befordra valfri aktivitets egenskap för pipeline som en användar egenskap så att den blir en entitet som du kan övervaka. Du kan till exempel flytta **käll** -och **mål** egenskaperna för kopierings aktiviteten i din pipeline som användar egenskaper. Du kan också välja **generera automatiskt** för att generera egenskaperna för **käll** -och **mål** användare för en kopierings aktivitet.

![Skapa användar egenskaper](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Du kan bara flytta upp till fem egenskaper för pipeline-aktiviteter som användar egenskaper.

När du har skapat användar egenskaperna kan du övervaka dem i vyerna i övervaknings listan. Om källan för kopierings aktiviteten är ett tabell namn kan du övervaka käll tabellens namn som en kolumn i list visningen för aktivitets körningar.

![Aktivitets körnings listan utan användar egenskaper](media/monitor-visually/monitor-user-properties-image2.png)

![Lägg till kolumner för användar egenskaper i listan aktivitets körningar](media/monitor-visually/monitor-user-properties-image3.png)

![Aktivitets körnings lista med kolumner för användar egenskaper](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Kör om aktiviteter i en pipeline

Nu kan du köra om aktiviteter i en pipeline. Välj **Visa aktivitets körningar**och välj sedan aktiviteten i din pipeline från vilken plats du vill köra pipelinen igen.

![Visa aktivitetskörningar](media/monitor-visually/rerun-activities-image1.png)

![Välj en aktivitets körning](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Visa körnings historik igen

Du kan visa körnings historiken för alla pipelines i list visningen.

![Visa historik](media/monitor-visually/rerun-history-image1.png)

Du kan också Visa körnings historiken för en viss pipeline-körning.

![Visa historik för en pipeline-körning](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-vyer

Använd Gantt-vyer för att snabbt visualisera dina pipeliner och aktivitets körningar. Du kan titta på vyn Gantt-visning per pipeline eller grupp efter anteckningar/taggar som du har skapat i pipelinen.

![Exempel på ett Gantt-diagram](media/monitor-visually/gantt1.png)

![Anteckningar i Gantt-diagram](media/monitor-visually/gantt2.png)

Längden på fältet informerar pipelinens varaktighet. Du kan också välja fältet för att se mer information.

![Varaktighet för Gantt-schema](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Guidade visningar
Välj **informations** ikonen längst ned till vänster. Välj sedan **guidade visningar** för att få stegvisa instruktioner om hur du övervakar pipeline och aktivitets körningar.

![Guidade visningar](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Feedback
Välj **feedback** -ikonen för att ge oss feedback om olika funktioner eller problem som du kan komma att behöva.

![Feedback](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Aviseringar

Du kan generera aviseringar för mått som stöds i Data Factory. Välj **övervaka** > **aviseringar & mått** på sidan Data Factory övervakning för att komma igång.

![Sidan Data Factory Monitor](media/monitor-visually/alerts01.png)

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Skapa aviseringar

1.  Välj **ny aviserings regel** för att skapa en ny avisering.

    ![Knappen Ny varnings regel](media/monitor-visually/alerts02.png)

1.  Ange regel namnet och välj allvarlighets grad för avisering.

    ![Rutor för regel namn och allvarlighets grad](media/monitor-visually/alerts03.png)

1.  Välj aviserings villkoret.

    ![Box för mål villkor](media/monitor-visually/alerts04.png)

    ![Lista över kriterier](media/monitor-visually/alerts05.png)

1.  Konfigurera aviserings logiken. Du kan skapa en avisering för det valda måttet för alla pipeliner och motsvarande aktiviteter. Du kan också välja en viss aktivitets typ, ett aktivitets namn, ett pipelin namn eller en feltyp.

    ![Alternativ för att konfigurera aviserings logik](media/monitor-visually/alerts06.png)

1.  Konfigurera e-post, SMS, push och röst meddelanden för aviseringen. Skapa en åtgärds grupp eller Välj en befintlig för aviserings meddelanden.

    ![Alternativ för att konfigurera meddelanden](media/monitor-visually/alerts07.png)

    ![Alternativ för att lägga till ett meddelande](media/monitor-visually/alerts08.png)

1.  Skapa varnings regeln.

    ![Alternativ för att skapa en varnings regel](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning och hantering av pipelines finns i artikeln [övervaka och hantera pipelines program mässigt](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
