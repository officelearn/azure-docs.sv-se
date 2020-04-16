---
title: Övervaka Azure Data Factory visuellt
description: Lär dig hur du övervakar Azure-datafabriker visuellt
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419450"
---
# <a name="visually-monitor-azure-data-factory"></a>Övervaka Azure Data Factory visuellt

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du har skapat och publicerat en pipeline i Azure Data Factory kan du associera den med en utlösare eller manuellt starta en ad hoc-körning. Du kan övervaka alla dina pipeline-körningar internt i azure data factory-användarupplevelsen. Om du vill öppna övervakningsupplevelsen väljer du panelen **Övervaka & Hantera** i datafabriksbladet i [Azure-portalen](https://portal.azure.com/). Om du redan är i ADF UX klickar du på **monitorikonen** på vänster sidofält.

Alla datafabrikskörningar visas i webbläsarens lokala tidszon. Om du ändrar tidszonen fäster alla datum-/tidsfält till den som du har valt.

## <a name="monitor-pipeline-runs"></a>Övervaka pipelinekörningar

Standardövervakningsvyn är en lista över pipelinekörningar under den valda tidsperioden. Följande kolumner visas:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Rörledningens namn |
| Åtgärder | Ikoner som gör att du kan visa aktivitetsinformation, avbryta eller köra pipelinen igen |
| Kör start | Startdatum och starttid för pipelinekörningen (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Varaktighet | Kör varaktighet (HH:MM:SS) |
| Utlöses av | Namnet på utlösaren som startade pipelinen |
| Status | **Misslyckades,** **lyckades**, **Pågår,** **Avbröts**eller **köade** |
| Anteckningar | Filterbara taggar som är associerade med en pipeline  |
| Parametrar | Parametrar för pipelinekörningen (namn/värdepar) |
| Fel | Om pipelinen misslyckades visas körningsfelet |
| Körnings-ID | ID för pipelinekörningen |

![Listvy för övervakning av pipeline-körningar](media/monitor-visually/pipeline-runs.png)

Du måste manuellt välja knappen **Uppdatera** för att uppdatera listan över pipeline- och aktivitetskörningar. Autorefresh stöds för närvarande inte.

![Knappen Uppdatera](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Övervaka aktivitetskörningar

Om du vill visa aktivitetskörningar för varje pipelinekörning väljer du ikonen **Visa aktivitetskörning** under kolumnen **Åtgärder.** Listvyn visar aktivitetskörningar som motsvarar varje pipeline-körning.

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namnet på aktiviteten i pipelinen |
| Aktivitetstyp | Typ av aktivitet, till exempel **Kopiera,** **ExecuteDataFlow**eller **AzureMLExecutePipeline** |
| Åtgärder | Ikoner som gör att du kan se JSON-inmatningsinformation, JSON-utdatainformation eller detaljerade aktivitetsspecifika övervakningsupplevelser | 
| Kör start | Startdatum och starttid för aktivitetskörningen (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Varaktighet | Kör varaktighet (HH:MM:SS) |
| Status | **Misslyckades,** **lyckades,** **pågår**eller **avbröts** |
| Körning för integrering | Vilken integrationskörning aktiviteten kördes på |
| Egenskaper för användare | Användardefinierade egenskaper för aktiviteten |
| Fel | Om aktiviteten misslyckades visas körningsfelet |
| Körnings-ID | ID för aktivitetskörningen |

![Listvy för övervakning av aktivitetskörningar](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Befordra användaregenskaper för att övervaka

Befordra alla pipeline-aktivitetsegenskap som en användaregenskap så att den blir en entitet som du övervakar. Du kan till exempel befordra egenskaperna **Källa** och **Mål** för kopieringsaktiviteten i pipelinen som användaregenskaper. Välj **Generera automatiskt** **om** du vill generera användaregenskaperna Källa och **Mål** för en kopieringsaktivitet.

![Skapa användaregenskaper](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Du kan bara befordra upp till fem pipeline-aktivitetsegenskaper som användaregenskaper.

När du har skapat användaregenskaperna kan du övervaka dem i övervakningslistevyerna. Om källan för kopieringsaktiviteten är ett tabellnamn kan du övervaka källtabellnamnet som en kolumn i listvyn för aktivitetskörningar.

![Lista över aktivitetskörningar utan användaregenskaper](media/monitor-visually/monitor-user-properties-image2.png)

![Lägga till kolumner för användaregenskaper i listan aktivitetskörning](media/monitor-visually/monitor-user-properties-image3.png)

![Lista över aktivitetskörningar med kolumner för användaregenskaper](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Konfigurera listvyn

### <a name="order-and-filter"></a>Ordning och filter

Växla om pipeline-körningar ska vara nedstigande eller stigande enligt starttiden för körningen. Filtrera pipeline körs med hjälp av följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Filtrera efter namnet på pipelinen. |
| Kör start |  Bestäm tidsintervallet för pipelinekörningarna som visas. Alternativen inkluderar snabbfilter för **Senaste 24 timmarna**, **Förra veckan**och **Senaste 30 dagarna** eller för att välja ett anpassat datum och en anpassad tid. |
| Kör status | Filter körs efter status: **Lyckades**, **Misslyckades,** **Köat,** **Avbruten**eller **Pågår**. |
| Anteckningar | Filtrera efter taggar som tillämpas på varje pipeline |
| Körningar | Filtrera om du vill se omartorledningar |

![Alternativ för filtrering](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Lägga till eller ta bort kolumner
Högerklicka på listvyrubriken och välj kolumner som du vill ska visas i listvyn.

![Alternativ för kolumner](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Justera kolumnbredder
Öka och minska kolumnbredderna i listvyn genom att hovra över kolumnrubriken.

## <a name="rerun-activities-inside-a-pipeline"></a>Köra om aktiviteter i en pipeline

Du kan köra aktiviteter igen i en pipeline. Välj **Visa aktivitetskörning**och välj sedan aktiviteten i pipelinen från vilken punkt du vill köra pipelinen igen.

![Visa aktivitetskörningar](media/monitor-visually/rerun-activities-image1.png)

![Välj en aktivitetskörning](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Kör från misslyckad aktivitet

Om en aktivitet misslyckas, time out eller avbryts kan du köra pipelinen från den misslyckade aktiviteten genom att välja **Kör igen från misslyckad aktivitet**.

![Återkörd misslyckades aktivitet](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Visa historik för repris

Du kan visa historiken för repris för alla pipeline-körningar i listvyn.

![Visa historik](media/monitor-visually/rerun-history-image1.png)

Du kan också visa körhistorik för en viss pipeline-körning.

![Visa historik för en pipeline-körning](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt-vyer

Använd Gantt-vyer för att snabbt visualisera dina pipelines och aktivitetskörningar.

![Exempel på ett Gantt-schema](media/monitor-visually/gantt1.png)

Du kan titta på Gantt-vyn per pipeline eller grupp efter anteckningar/taggar som du har skapat på dina pipelines.

![Gantt-diagramanteckningar](media/monitor-visually/gantt2.png)

Längden på baren informerar om rörledningens varaktighet. Du kan också välja stapeln för att se mer information.

![Varaktighet för Gantt-schema](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Guidade turer
Välj **ikonen Information** längst ned till vänster. Välj sedan **Guidade turer** för att få steg-för-steg-instruktioner om hur du övervakar din pipeline och aktivitetskörning.

![Guidade turer](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Aviseringar

Du kan höja aviseringar om mått som stöds i Data Factory. Välj **Övervaka** > aviseringar & mått på**övervakningssidan** för datafabriken för att komma igång.

![Sidan Övervakare för datafabrik](media/monitor-visually/alerts01.png)

För en sju minuters introduktion och demonstration av den här funktionen, titta på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Skapa aviseringar

1.  Välj **Ny aviseringsregel** om du vill skapa en ny avisering.

    ![Knappen Ny varningsregel](media/monitor-visually/alerts02.png)

1.  Ange regelnamnet och välj allvarlighetsgrad för avisering.

    ![Rutor för regelnamn och allvarlighetsgrad](media/monitor-visually/alerts03.png)

1.  Välj varningsvillkor.

    ![Ruta för målkriterier](media/monitor-visually/alerts04.png)

    ![Lista över kriterier](media/monitor-visually/alerts05.png)

1.  Konfigurera varningslogiken. Du kan skapa en avisering för det valda måttet för alla pipelines och motsvarande aktiviteter. Du kan också välja en viss aktivitetstyp, aktivitetsnamn, pipelinenamn eller feltyp.

    ![Alternativ för att konfigurera varningslogik](media/monitor-visually/alerts06.png)

1.  Konfigurera e-post-, SMS-, push- och röstaviseringar för aviseringen. Skapa en åtgärdsgrupp, eller välj en befintlig, för varningsmeddelandena.

    ![Alternativ för att konfigurera meddelanden](media/monitor-visually/alerts07.png)

    ![Alternativ för att lägga till ett meddelande](media/monitor-visually/alerts08.png)

1.  Skapa aviseringsregeln.

    ![Alternativ för att skapa en varningsregel](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du övervakar och hanterar pipelines finns i artikeln [Övervaka och hantera pipelines programmässigt.](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)
