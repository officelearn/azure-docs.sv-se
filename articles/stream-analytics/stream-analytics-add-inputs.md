---
title: Lägga till en data-indata till Stream Analytics-jobb | Microsoft Docs
description: Lär dig mer om att koppla samman en datakälla till Stream Analytics-jobbet som strömmande data i indata från Händelsehubbar eller referens data från blogg lagring.
keywords: indata, strömmande data
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
editor: ''
ms.assetid: 9e59bd24-2a80-4ecb-b6b2-309a07c70bcd
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: ca96f0a9abc13cd5d3cc93210c0347064c6503b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Lägg till en strömmande data indata eller referens data till ett Stream Analytics-jobb
Lär dig mer om att koppla samman en datakälla till Stream Analytics-jobbet som strömmande data i indata från Händelsehubbar eller referens data från Blob storage.

Azure Stream Analytics-jobb kan vara ansluten till en inmatning eller mer, som definierar en anslutning till en befintlig datakälla. Det är används av Stream Analytics-jobbet och behandlas i realtid som strömmande data som data skickas till datakällan. Stream Analytics har förstklassigt integrering med [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) och [Azure Blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md) både inom och utanför jobbets prenumeration.

Den här artikeln är ett steg i den [Stream Analytics-Utbildningsväg](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Indata: data och referensdata
Det finns två olika typer av indata i Stream Analytics: dataströmmar och referensdata.

* **Dataströmmar**: Stream Analytics-jobb måste innehålla minst en inkommande dataström som ska konsumeras och transformeras av jobbet. Azure Blob storage och Azure Event Hubs kan användas som indata datakällor för dataströmmen. Händelsehubbar i Azure används för att samla in händelseströmmar från anslutna enheter, tjänster och program. Azure Blob storage kan användas som en Indatakällan för att föra in stora mängder data som en dataström.  
* **Referensdata**: Stream Analytics stöder en andra typen av extra inkommande kallas referensdata.  Till skillnad från data i rörelse är dessa data statiska eller sakta ändra.  Den används vanligtvis för att utföra look-ups och samband med dataströmmar för att skapa en större mängd data.  Azure Blob storage är för närvarande endast stöds Indatakällan för referensdata.  

Lägga till indata till Stream Analytics-jobbet:

1. I Azure portal klickar du på **indata** och klicka sedan på **lägga till indata** i Stream Analytics-jobbet.
   
    ![Azure portal – lägga till indata.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    I Azure portal klickar du på den **indata** panelen i Stream Analytics-jobbet.  
   
    ![Azure portal – lägga till indata.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Ange vilken typ av indata: antingen **dataströmmen** eller **referensdata**.
   
    ![Lägga till korrekta data indata, strömmas eller refererar till](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Lägga till korrekta data indata, strömmas eller refererar till](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Om du skapar en dataström inmatning, ange källtyp för indata.  Det här steget kan hoppas över när referensdata skapas som endast Blob-lagring stöds just nu.
   
    ![Lägg till inkommande dataström data](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Lägg till inkommande dataström data](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Ange ett eget namn för den här indata i rutan inmatat Alias.  Det här namnet används i ditt jobb frågan vid ett senare tillfälle för att referera till indata.
   
    Fyll i resten av egenskaperna behövs för att ansluta till datakällan. De här fälten varierar beroende på typ av indata- och och definieras i detalj [här](stream-analytics-create-a-job.md).  
   
    ![Lägga till event hub data indata](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Ange inställningar för serialisering för indata:
   
   * Om du vill kontrollera att dina frågor fungerar som förväntat, ange den **händelse serialiseringsformat** för inkommande data.  Stöds serialisering format är JSON-, CSV- och Avro.
   * Kontrollera den **kodning** för data.  UTF-8 är det enda kodformat som stöds för närvarande.
     
     ![Inställningar för serialisering av data för indata](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Inställningar för serialisering av data för indata](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. När du har slutfört inkommande skapa verifierar Stream Analytics att den kan ansluta till Indatakällan.  Du kan visa statusen på Testa anslutning igen i meddelandehubben.
   
    ![Testa anslutningen för strömmande data som indata](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Testa anslutningen för strömmande data som indata](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Få hjälp med streaming indata
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

