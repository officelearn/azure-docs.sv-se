---
title: Hur du konfigurerar data matar ut för Azure Stream Analytics-jobb
description: Konfigurera utdata för Stream Analytics-jobb | Learning sökvägssegment.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/26/2017
ms.openlocfilehash: 2e54b7970e4748ff56844fb06fa5177ddb207fa6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Hur du konfigurerar data matar ut för Stream Analytics-jobb

Azure Stream Analytics-jobb kan vara ansluten till en eller flera utdata som definierar en anslutning till en befintlig data sink. Stream Analytics-jobbet, processer och omvandlar inkommande data som skrivs en dataström med data utdata händelser till ditt jobb utdata.

Stream Analytics-data utdata kan användas som källa för realtids-instrumentpaneler eller aviseringar, utlöser arbetsflöden för flytt av data eller bara arkiverar data för batch-bearbetning vid ett senare tillfälle. Stream Analytics har förstklassigt integrering med flera Azure-tjänster som dokumenteras här.

Lägga till utdata till Stream Analytics-jobb:

1. I den [Azure-portalen](https://portal.azure.com), öppna ditt jobb och klickar på **utdata** och klicka sedan på **Lägg till** i bladet utdata som visas.
   
    ![Lägga till utdata](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. Ange ett eget namn för den här utdata i den **kolumnalias** rutan. Det här namnet kan användas i ditt jobb fråga vid ett senare tillfälle för att referera till utdata.  
   
    Fyll i resten av egenskaperna behövs för att ansluta till dina utdata.  De här fälten varierar beroende på utdatatypen och definieras här.  
   
    ![Välj typ av data movement](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. Beroende på vilken Utdatatyp, kan du behöva ange hur data serialiseras eller formaterad. Här beskrivs specifika serialisering inställningarna för varje Utdatatyp av.
   
    Fyll i resten av egenskaperna behövs för att ansluta till datakällan. De här fälten varierar beroende på typ av indata- och och definieras i detalj i den [skapa jobbet artikel](stream-analytics-create-a-job.md).  

> [!Note]
>
> En output-elementet som läggs till projektet, måste finnas innan jobbet har startats och händelser börjar flöda. Till exempel om du använder Blob storage som utdata jobbet kommer inte att skapa ett lagringskonto automatiskt. Den måste skapas av användaren innan ASA jobbet har startats.
> 
 

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

