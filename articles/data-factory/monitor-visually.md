---
title: Övervaka Azure datafabriker visuellt | Microsoft Docs
description: Lär dig att övervaka visuellt Azure datafabriker
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: shlo
ms.openlocfilehash: b67c384ffd04176653ad434d39361ee67dc1ffea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="visually-monitor-azure-data-factories"></a>Visuellt övervaka Azure datafabriker
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda.
I den här snabbstartsguide du lära dig hur du övervakar data factory v2 pipelines visuellt utan att skriva en enda rad kod.
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [övervaka och hantera pipelines i Data Factory version1](v1/data-factory-monitor-manage-app.md).

## <a name="monitor-data-factory-v2-pipelines"></a>Övervaka data factory v2 pipelines

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Logga in på den [Azure-portalen](https://portal.azure.com/).
3. Navigera till bladet skapade data factory i Azure-portalen och klicka på panelen 'Övervaka och hantera'. Detta startar ADF v2 visual övervakningen.

## <a name="list-view-monitoring"></a>Listvyn övervakning

Övervaka pipeline och aktiviteter körs med en enkel lista Visa gränssnitt. Alla körningar visas i webbläsarens lokala tidszon. Om du ändrar tidszon anpassas alla fält med datum och tider automatiskt.  

#### <a name="monitoring-pipeline-runs"></a>Övervaka pipelinen körs
Listvy för varje pipelinekörning i dina Data Factory v2-pipelines. Inkluderade kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namnet på pipeline. |
| Åtgärder | Enskild åtgärd för att visa aktivitet körs. |
| Att köra Start | Pipeline kör start-datum och tid (MM/DD/ÅÅÅÅ HH: mm: SS AM/PM) |
| Varaktighet | Kör varaktighet (: mm: ss) |
| Aktiverad av | Manuell utlösning, schema utlösare |
| Status | Kunde inte lyckades pågår |
| Parametrar | Pipeline kör parametrar (namn, värde-par) |
| Fel | Pipeline kör fel (om/any) |
| Körnings-ID | ID för den pipeline som kör |

![Övervaka pipelinekörningar](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Övervaka aktiviteten körs
Listvy med aktivitetskörningar som motsvarar respektive pipelinekörning. Klicka på **aktiviteten körs** ikon under den **'Åtgärder'** kolumnen visa aktiviteten körs för varje pipelinen körs. Inkluderade kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namn på aktivitet i pipelinen. |
| Aktivitetstyp | Typen för aktiviteten d.v.s. kopia, HDInsightSpark, HDInsightHive osv. |
| Att köra Start | Kör aktiviteten starta datum och tid (MM/DD/ÅÅÅÅ HH: mm: SS AM/PM) |
| Varaktighet | Kör varaktighet (: mm: ss) |
| Status | Kunde inte lyckades pågår |
| Indata | JSON-matris som beskriver aktivitetens indata |
| Resultat | JSON-matris som beskriver aktiviteten matar ut |
| Fel | Aktiviteten kör fel (om/any) |

![Övervaka aktivitetskörningar](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Du måste klicka på **”uppdatera”** ikonen längst upp för att uppdatera listan över pipeline och aktiviteter körs. Automatisk uppdatering stöds inte för tillfället.
>

![Uppdatera](media/monitor-visually/refresh.png)

## <a name="features"></a>Funktioner

#### <a name="rich-ordering-and-filtering"></a>Funktionsrik sortering och filtrering

Ordning pipeline körs i desc/asc genom att köra Start och filter pipeline körs med följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namnet på pipeline. Alternativ inkluderar snabbfilter för ”senaste 24 timmarna', 'Förra veckan' senaste 30 dagarna eller välj en anpassad tidsvärdet. |
| Att köra Start | Pipeline kör start datum och tid |
| Kör Status | Filter körs efter status d.v.s. lyckades, misslyckades, pågår |

![Filter](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Lägg till/ta bort kolumner i listvyn
Högerklicka på listan Visa sidhuvud och Välj kolumner som ska visas i listan

![Kolumner](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Ordna om kolumnbredder i listvy
Öka och minska kolumnbredd i listan genom att helt enkelt hovra över kolumnrubriken

#### <a name="select-data-factory"></a>Välja datafabrik
Hovra över ikonen Data Factory på upp till vänster. Klicka på ' pilikonen ' att se en lista över azure-prenumerationer och data fabriker som du kan övervaka.

![Välja datafabrik](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Interaktiv visningar
Klicka på 'Informationsikonen' i nedre vänstra hörnet och klicka på 'Interaktiv visningar' för att få stegvisa instruktioner för hur du övervakar din pipeline och aktiviteter körs.

![Interaktiv visningar](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Feedback
Klicka på ikonen ”Feedback” om du vill ge oss feedback på olika funktioner eller eventuella problem som du kan hantera.

![Feedback](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Nästa steg

Se [övervaka och hantera pipelines programmässigt](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) artikeln innehåller information om övervakning och hantering av pipelines
