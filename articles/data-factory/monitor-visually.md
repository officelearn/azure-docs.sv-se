---
title: Övervaka Azure-datafabriker visuellt | Microsoft Docs
description: Lär dig hur du övervakar Azure-datafabriker visuellt
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
ms.openlocfilehash: df684860cd3d1b6a002a300682ca4c6398461ba6
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54409970"
---
# <a name="visually-monitor-azure-data-factories"></a>Övervaka Azure-datafabriker visuellt
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda.

I den här snabbstarten får du lära dig hur visuellt övervaka Data Factory-pipelines utan att behöva skriva en enda rad kod.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="monitor-data-factory-pipelines"></a>Övervaka Data Factory-pipelines

Övervaka pipelinen och aktivitetskörningar med en enkel lista Visa gränssnitt. Alla körningar visas i webbläsarens lokala tidszon. Du kan ändra den aktuella tidszonen och alla fält med datum Fäst till den valda tidszonen.  

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Logga in på den [Azure-portalen](https://portal.azure.com/).
3. Navigera till skapade data factory-bladet i Azure-portalen och klicka på panelen, övervaka och hantera ”för att starta Data Factory visual övervakningen.

## <a name="monitor-pipeline-runs"></a>Övervaka pipelinekörningar
Listvy för varje pipelinekörning i dina Data Factory v2-pipelines. Inkluderade kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Namn på pipeline | Namnet på pipeline. |
| Åtgärder | Enkel åtgärd för att visa de aktivitetskörningar. |
| Kör Start | Pipeline-körning start datum tid (MM/DD/ÅÅÅÅ HH: mm: SS AM/PM) |
| Varaktighet | Körningens varaktighet (: mm: ss) |
| Aktiverad av | Manuell utlösare, schemautlösare |
| Status | Misslyckades, lyckades, pågår |
| Parametrar | Parametrar (namn, värde-par) för pipelinekörning |
| Fel | Pipelinekörning fel (if/any) |
| Körnings-ID | ID för pipelinekörningen |

![Övervaka pipelinekörningar](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Övervaka aktivitetskörningar
Listvy med aktivitetskörningar som motsvarar respektive pipelinekörning. Klicka på **aktiviteten körs** ikonen under den **”åtgärder”** kolumn att visa aktivitet körs för varje pipelinekörning. Inkluderade kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namnet på aktiviteten i pipelinen. |
| Aktivitetstyp | Typ av aktivitet, till exempel kopiera, HDInsightSpark, HDInsightHive osv. |
| Kör Start | Aktivitetskörning start datum tid (MM/DD/ÅÅÅÅ HH: mm: SS AM/PM) |
| Varaktighet | Körningens varaktighet (: mm: ss) |
| Status | Misslyckades, lyckades, pågår |
| Indata | JSON-matris som beskriver aktivitetsindata |
| Resultat | JSON-matris som beskriver aktivitetsutdata |
| Fel | Aktivitetskörningsfel (if/any) |

![Övervaka aktivitetskörningar](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Du måste klicka på **”uppdatera”** ikonen längst upp för att uppdatera listan över pipelinen och aktivitetskörningar. Automatisk uppdatering stöds inte för tillfället.

![Uppdatera](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Välj en data factory för att övervaka
Hovra över den **Data Factory** ikonen uppe till vänster. Klicka på ikonen ”pilen” för att se en lista över azure-prenumerationer och datafabriker som du kan övervaka.

![Välja datafabrik](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Konfigurera listvyn

### <a name="apply-rich-ordering-and-filtering"></a>Använd omfattande sortering och filtrering

Sortera pipelinekörningar i desc/asc för med och filtrera pipelinekörningar efter följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Namn på pipeline | Namnet på pipeline. Alternativ inkluderar snabbfilter för ”senaste 24 timmarna', 'Förra veckan', 'senaste 30 dagarna, eller välj ett anpassat datum-tid. |
| Kör Start | Pipelinekörning start datum tid |
| Körningsstatus | Filter som körs efter status - lyckades, misslyckades, pågår |

![Filter](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Lägg till eller ta bort kolumner
Högerklicka på listan Visa rubriken och välj vilka kolumner som du vill ska visas i listvyn

![Kolumner](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Justera kolumnbredder
Öka och minska kolumnbredder i listvyn genom att hovra över kolumnrubriken

## <a name="promote-user-properties-to-monitor"></a>Flytta upp användaregenskaper att övervaka

Du kan flytta upp en egenskap för pipeline-aktivitet som en användaregenskap så att det blir en entitet som du kan övervaka. Du kan exempelvis flytta upp den **källa** och **mål** egenskaperna för kopieringsaktiviteten i pipelinen som Användaregenskaper. Du kan också välja **Autogenererad** att generera den **källa** och **mål** användaregenskaper för en Kopieringsaktivitet.

![Skapa användaregenskaper](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Du kan bara flytta upp egenskaper för upp till 5 pipeline-aktivitet som Användaregenskaper.

När du har skapat användaregenskaperna kan övervaka du dem i vyerna övervakning lista. Om källan för aktiviteten kopiera är ett tabellnamn kan övervaka du namn på källtabellen som en kolumn i aktiviteten körs listvy.

![Lista med utan användaregenskaper aktivitetskörningar](media/monitor-visually/monitor-user-properties-image2.png)

![Lägga till kolumner för användaregenskaper i aktivitetslista för körningar](media/monitor-visually/monitor-user-properties-image3.png)

![Aktivitetskörningar lista med kolumner för användaregenskaper](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Kör aktiviteter i en pipeline

Du kan nu köra om aktiviteter i en pipeline. Klicka på **visa de aktivitetskörningar** och väljer du en aktivitet i din pipeline från vilken punkt du vill köra din pipeline.

![Visa aktivitetskörningar](media/monitor-visually/rerun-activities-image1.png)

![Välj en aktivitet som körs](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Visa kör historik

Du kan visa kör historiken för alla pipelinekörningar i listvyn.

![Visa historik](media/monitor-visually/rerun-history-image1.png)

Du kan också visa kör historik för en viss pipelinekörning.

![Visa historik för en pipelinekörning](media/monitor-visually/rerun-history-image2.png)

## <a name="guided-tours"></a>Guidad visningar
Klicka på ”informationsikonen' i nedre vänstra hörnet och klicka på” Guidad visningar ”för att få stegvisa instruktioner för hur du övervakar din pipelinen och aktivitetskörningar.

![Guidad visningar](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Feedback
Klicka på ikonen ”Feedback” för att ge oss feedback om olika funktioner eller eventuella problem som du kanske står inför.

![Feedback](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Aviseringar

Du kan generera aviseringar på mått som stöds i Data Factory. Välj **övervakaren -> aviseringar och mått** på sidan Övervakare för Data Factory för att komma igång.

![](media/monitor-visually/alerts01.png)

Titta på följande videoklipp för en sju minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Skapa aviseringar

1.  Klicka på **ny aviseringsregel** att skapa en ny avisering.

    ![](media/monitor-visually/alerts02.png)

1.  Ange Regelnamnet på och väljer du aviseringen **allvarlighetsgrad**.

    ![](media/monitor-visually/alerts03.png)

1.  Välj aviseringsvillkoren.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Konfigurera Alert logic. Du kan skapa en avisering för det valda måttet för alla pipelines och motsvarande aktiviteter. Du kan också välja en viss aktivitetstyp, aktivitetsnamn, namn på pipeline eller en feltyp av.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurera **e-post/SMS/Push/röst** meddelanden för aviseringen. Skapa eller välj en befintlig **åtgärdsgrupp** för aviseringar skickas.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Skapa varningsregeln.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Nästa steg

Se [övervaka och hantera pipelines programmässigt](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) du lär dig om att övervaka och hantera pipelines.
