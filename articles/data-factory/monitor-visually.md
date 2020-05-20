---
title: Övervaka Azure Data Factory visuellt
description: Lär dig att visuellt övervaka Azures data fabriker
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 9a81ab58335e968e5ce4816de85ed1b63bc93455
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699601"
---
# <a name="visually-monitor-azure-data-factory"></a>Övervaka Azure Data Factory visuellt

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du har skapat och publicerat en pipeline i Azure Data Factory kan du associera den med en utlösare eller manuellt starta en ad hoc-körning. Du kan övervaka alla dina pipelines körs internt i Azure Data Factory användar upplevelse. Öppna övervaknings upplevelsen genom att välja panelen **övervaka & hantera** på Data Factory-bladet i [Azure Portal](https://portal.azure.com/). Om du redan befinner dig i ADF-UX klickar du på ikonen **övervaka** på den vänstra panelen.

Alla data Factory-körningar visas i webbläsarens lokala tidszon. Om du ändrar tids zonen fästs alla datum/tid-fält på den som du har valt.

## <a name="monitor-pipeline-runs"></a>Övervaka pipelinekörningar

Vyn standard övervakning är en lista över pipelines som körs under den valda tids perioden. Följande kolumner visas:

| **Kolumn namn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namn på pipelinen |
| Åtgärder | Ikoner som låter dig Visa aktivitets information, avbryta eller köra pipelinen igen |
| Kör Start | Start datum och-tid för pipeline-körningen (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Varaktighet | Körnings tid (HH: MM: SS) |
| Utlöst av | Namnet på utlösaren som startade pipelinen |
| Status | **Misslyckades**, **lyckades**, **In Progress**pågår, **avbröts**eller **köade** |
| Anteckningar | Filter bara taggar som är associerade med en pipeline  |
| Parametrar | Parametrar för pipeline-körningen (namn/värde-par) |
| Fel | Om pipelinen misslyckades, körs fel |
| Körnings-ID | ID för pipeline-körningen |

![Listvy för övervakning av pipeline-körningar](media/monitor-visually/pipeline-runs.png)

Du måste manuellt välja knappen **Uppdatera** för att uppdatera listan över pipeline-och aktivitets körningar. Autouppdatering stöds inte för närvarande.

![Knappen Uppdatera](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Övervaka aktivitetskörningar

Om du vill visa aktivitets körningar för varje pipeline-körning väljer du ikonen **Visa aktivitet körs** under kolumnen **åtgärder** . Listvyn visar aktivitets körningar som motsvarar varje pipeline-körning.

| **Kolumn namn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namn på aktiviteten inuti pipelinen |
| Aktivitetstyp | Typ av aktivitet, till exempel **copy**, **ExecuteDataFlow**eller **AzureMLExecutePipeline** |
| Åtgärder | Ikoner som gör att du kan se information om JSON-indata, JSON-utdata eller detaljerade verksamhetsspecifika övervaknings upplevelser | 
| Kör Start | Start datum och-tid för aktivitets körningen (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Varaktighet | Körnings tid (HH: MM: SS) |
| Status | **Misslyckades**, **lyckades**, **pågår**eller **avbröts** |
| Integration Runtime | Som Integration Runtime aktiviteten kördes på |
| Egenskaper för användare | Användardefinierade egenskaper för aktiviteten |
| Fel | Om aktiviteten misslyckades, körs fel |
| Körnings-ID | ID för aktivitets körning |

![Listvy för övervakning av aktivitets körningar](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Befordra användar egenskaper för övervakning

Höj en pipeline-aktivitets egenskap som en användar egenskap så att den blir en entitet som du övervakar. Du kan till exempel flytta **käll** -och **mål** egenskaperna för kopierings aktiviteten i din pipeline som användar egenskaper. Välj **generera automatiskt** för att generera egenskaperna för **käll** -och **mål** användare för en kopierings aktivitet.

![Skapa användar egenskaper](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Du kan bara flytta upp till fem egenskaper för pipeline-aktiviteter som användar egenskaper.

När du har skapat användar egenskaperna kan du övervaka dem i vyerna i övervaknings listan. Om källan för kopierings aktiviteten är ett tabell namn kan du övervaka käll tabellens namn som en kolumn i list visningen för aktivitets körningar.

![Aktivitets körnings listan utan användar egenskaper](media/monitor-visually/monitor-user-properties-image2.png)

![Lägg till kolumner för användar egenskaper i listan aktivitets körningar](media/monitor-visually/monitor-user-properties-image3.png)

![Aktivitets körnings lista med kolumner för användar egenskaper](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Konfigurera listvyn

### <a name="order-and-filter"></a>Ordning och filter

Växla om pipeline-körningar ska ske i fallande eller stigande ordning enligt körningens start tid. Filtrera Pipeline-körningar med hjälp av följande kolumner:

| **Kolumn namn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Filtrera efter namnet på pipelinen. |
| Kör Start |  Bestäm tidsintervallet för pipelinen som visas. Alternativen inkluderar snabb filter för de **senaste 24 timmarna**, **senaste veckan**och de **senaste 30 dagarna** , eller för att välja ett anpassat datum och tid. |
| Körnings status | Filter körs efter status: **lyckades**, **misslyckades**, **köade**, **avbröts**eller **pågår**. |
| Anteckningar | Filtrera efter taggar som används för varje pipeline |
| Körningar | Filtrera om du vill se reran-pipeliner |

![Alternativ för filtrering](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Lägga till eller ta bort kolumner
Högerklicka på rubriken för listvyn och Välj kolumner som du vill ska visas i listvyn.

![Alternativ för kolumner](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Justera kolumn bredder
Öka och minska kolumn bredderna i listvyn genom att hovra över kolumn rubriken.

## <a name="rerun-activities-inside-a-pipeline"></a>Kör om aktiviteter i en pipeline

Du kan köra om aktiviteter i en pipeline. Välj **Visa aktivitets körningar**och välj sedan aktiviteten i din pipeline från vilken plats du vill köra pipelinen igen.

![Visa aktivitetskörningar](media/monitor-visually/rerun-activities-image1.png)

![Välj en aktivitets körning](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Kör om från misslyckad aktivitet

Om en aktivitet Miss lyckas, tids gränsen uppnåddes eller avbryts, kan du köra pipelinen igen från den misslyckade aktiviteten genom att välja **Kör om från misslyckad aktivitet**.

![Kör om misslyckad aktivitet](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Visa körnings historik igen

Du kan visa körnings historiken för alla pipelines i list visningen.

![Visa historik](media/monitor-visually/rerun-history-image1.png)

Du kan också Visa körnings historiken för en viss pipeline-körning.

![Visa historik för en pipeline-körning](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>Övervaka förbrukning

Du kan se de resurser som förbrukas av en pipeline-körning genom att klicka på förbruknings ikonen bredvid kör. 

![Övervaka förbrukning](media/monitor-visually/monitor-consumption-1.png)

Om du klickar på ikonen öppnas en förbruknings rapport över resurser som används av pipeline-körningen. 

![Övervaka förbrukning](media/monitor-visually/monitor-consumption-2.png)

Du kan koppla värdena till [pris Kalkylatorn för Azure]. ( https://azure.microsoft.com/pricing/details/data-factory/) för att beräkna kostnaden för pipeline-körningen. Mer information om Azure Data Factory priser finns i [förstå priser](pricing-concepts.md).

> [!NOTE]
> De här värdena som returneras av pris kalkylatorn är en uppskattning. Det visar inte exakt det belopp du debiteras per Azure Data Factory 

## <a name="gantt-views"></a>Gantt-vyer

Använd Gantt-vyer för att snabbt visualisera dina pipeliner och aktivitets körningar.

![Exempel på ett Gantt-diagram](media/monitor-visually/gantt1.png)

Du kan titta på vyn Gantt-visning per pipeline eller grupp efter anteckningar/taggar som du har skapat i pipelinen.

![Anteckningar i Gantt-diagram](media/monitor-visually/gantt2.png)

Längden på fältet informerar pipelinens varaktighet. Du kan också välja fältet för att se mer information.

![Varaktighet för Gantt-schema](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Guidade visningar
Välj **informations** ikonen längst ned till vänster. Välj sedan **guidade visningar** för att få stegvisa instruktioner om hur du övervakar pipeline och aktivitets körningar.

![Guidade visningar](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Aviseringar

Du kan generera aviseringar för mått som stöds i Data Factory. Välj **övervaka**  >  **aviseringar & mått** på sidan Data Factory övervakning för att komma igång.

![Sidan Data Factory Monitor](media/monitor-visually/alerts01.png)

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Skapa aviseringar

1.  Välj **ny aviserings regel** för att skapa en ny avisering.

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
