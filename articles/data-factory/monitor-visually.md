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
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: shlo
ms.openlocfilehash: 4b3828e1857d17a128de346449d5cf2041709e50
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041083"
---
# <a name="visually-monitor-azure-data-factories"></a>Övervaka Azure-datafabriker visuellt
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda.
I den här snabbstartsguiden ska lära du dig att visuellt övervaka data factory v2-pipelines utan att behöva skriva en enda rad kod.
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="monitor-data-factory-v2-pipelines"></a>Övervaka data factory v2-pipelines

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Logga in på den [Azure-portalen](https://portal.azure.com/).
3. Navigera till skapade data factory-bladet i Azure-portalen och klicka på panelen, övervaka och hantera'. Nu öppnas ADF v2 visual övervakningen.

## <a name="list-view-monitoring"></a>Listvy – övervakning

Övervaka pipelinen och aktivitetskörningar med en enkel lista Visa gränssnitt. Alla körningar visas i webbläsarens lokala tidszon. Om du ändrar tidszon anpassas alla fält med datum och tider automatiskt.  

#### <a name="monitoring-pipeline-runs"></a>Övervaka Pipelinekörningar
Listvy för varje pipelinekörning i dina Data Factory v2-pipelines. Inkluderade kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Namn på pipeline | Namnet på pipeline. |
| Åtgärder | Enkel åtgärd för att visa de aktivitetskörningar. |
| Kör Start | Pipeline-körning start datum tid (MM/DD/ÅÅÅÅ HH: mm: SS AM/PM) |
| Längd | Körningens varaktighet (: mm: ss) |
| Aktiverad av | Manuell utlösare, schemautlösare |
| Status | Misslyckades, lyckades, pågår |
| Parametrar | Parametrar (namn, värde-par) för pipelinekörning |
| Fel | Pipelinekörning fel (if/any) |
| Körnings-ID | ID för pipelinekörningen |

![Övervaka pipelinekörningar](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Övervaka Aktivitetskörningar
Listvy med aktivitetskörningar som motsvarar respektive pipelinekörning. Klicka på **aktiviteten körs** ikonen under den **”åtgärder”** kolumn att visa aktivitet körs för varje pipelinekörning. Inkluderade kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namnet på aktiviteten i pipelinen. |
| Aktivitetstyp | Typ av aktivitet d.v.s. kopia, HDInsightSpark, HDInsightHive osv. |
| Kör Start | Aktivitetskörning start datum tid (MM/DD/ÅÅÅÅ HH: mm: SS AM/PM) |
| Längd | Körningens varaktighet (: mm: ss) |
| Status | Misslyckades, lyckades, pågår |
| Indata | JSON-matris som beskriver aktivitetsindata |
| Utdata | JSON-matris som beskriver aktivitetsutdata |
| Fel | Aktivitetskörningsfel (if/any) |

![Övervaka aktivitetskörningar](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Du måste klicka på **”uppdatera”** ikonen längst upp för att uppdatera listan över pipelinen och aktivitetskörningar. Automatisk uppdatering stöds inte för tillfället.
>

![Uppdatera](media/monitor-visually/refresh.png)

## <a name="features"></a>Funktioner

#### <a name="select-a-data-factory-to-monitor"></a>Välj en data factory för att övervaka
Hovra över den **Data Factory** ikonen uppe till vänster. Klicka på ikonen ”pilen” för att se en lista över azure-prenumerationer och datafabriker som du kan övervaka.

![Välj datafabrik](media/monitor-visually/select-datafactory.png)

#### <a name="rich-ordering-and-filtering"></a>Funktionsrik sortering och filtrering

Sortera pipelinekörningar i desc/asc för med och filtrera pipelinekörningar efter följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Namn på pipeline | Namnet på pipeline. Alternativ inkluderar snabbfilter för ”senaste 24 timmarna', 'Förra veckan', 'senaste 30 dagarna, eller välj ett anpassat datum-tid. |
| Kör Start | Pipelinekörning start datum tid |
| Körningsstatus | Filtret körs efter status d.v.s. lyckades, misslyckades, pågår |

![Filtrera](media/monitor-visually/filter.png)

#### <a name="addremove-columns-in-list-view"></a>Lägg till/ta bort kolumner i listvyn
Högerklicka på listan Visa sidhuvud och välja kolumner som du vill ska visas i listvyn

![Kolumner](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Ändra ordning på kolumnbredder i listvyn
Öka och minska kolumnbredder i listvyn genom att hovra över kolumnrubriken

#### <a name="user-properties"></a>Användaregenskaper

Du kan flytta upp en egenskap för pipeline-aktivitet som en användaregenskap så att det blir en entitet som du kan övervaka. Du kan exempelvis flytta upp den **källa** och **mål** egenskaperna för kopieringsaktiviteten i pipelinen som Användaregenskaper. Du kan också välja **Autogenererad** att generera den **källa** och **mål** användaregenskaper för en Kopieringsaktivitet.

![Skapa användaregenskaper](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Du kan bara flytta upp egenskaper för upp till 5 pipeline-aktivitet som Användaregenskaper.

När du har skapat användaregenskaperna kan övervaka du dem i vyerna övervakning lista. Om källan för aktiviteten kopiera är ett tabellnamn kan övervaka du namn på källtabellen som en kolumn i aktiviteten körs listvy.

![Lista med utan användaregenskaper aktivitetskörningar](media/monitor-visually/monitor-user-properties-image2.png)

![Lägga till kolumner för användaregenskaper i aktivitetslista för körningar](media/monitor-visually/monitor-user-properties-image3.png)

![Aktivitetskörningar lista med kolumner för användaregenskaper](media/monitor-visually/monitor-user-properties-image4.png)

#### <a name="guided-tours"></a>Guidad visningar
Klicka på ”informationsikonen' i nedre vänstra hörnet och klicka på” Guidad visningar ”för att få stegvisa instruktioner för hur du övervakar din pipelinen och aktivitetskörningar.

![Guidad visningar](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Feedback
Klicka på ikonen ”Feedback” för att ge oss feedback om olika funktioner eller eventuella problem som du kanske står inför.

![Feedback](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Nästa steg

Se [övervaka och hantera pipelines programmässigt](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) du lär dig om att övervaka och hantera pipelines
