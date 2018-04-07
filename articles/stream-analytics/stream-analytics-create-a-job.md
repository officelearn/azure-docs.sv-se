---
title: Skapa ett data analytics-jobb för bearbetning för Azure Stream Analytics
description: Skapa ett data analytics-jobb för bearbetning för Stream Analytics | Learning sökvägssegment.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 349d99a003df4fd211842033a467aa3051b50c77
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Skapa ett data analytics-jobb för bearbetning för Stream Analytics
Resursen på den översta nivån i Azure Stream Analytics är en Stream Analytics-jobbet.  Det består av en eller flera inkommande datakällor, en fråga uttrycka data transformation och ett eller flera mål för utdata som resultat har skrivits till. Tillsammans gör dessa att användaren kan utföra dataanalys bearbetning för strömmande datascenarier.

Om du vill börja använda Stream Analytics, börjar du med att skapa ett nytt Stream Analytics-jobb.  Observera att den här åtgärden har ingen fakturering effekter tills jobbet har startats.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **skapar du en resurs** > **Data + analys** > **Stream Analytics-jobbet**.
3. Välj **Skapa**.
   
3. Ange önskad konfiguration för Stream Analytics-jobbet.
   
   * I den **jobbnamn** anger du ett namn som identifierar Stream Analytics-jobbet. När den **jobbnamn** har verifierats, visas en grön kryssmarkering i jobbnamn. Den **jobbnamn** får bara innehålla alfanumeriska tecken och '-' tecken och måste vara mellan 3 och 63 tecken.
   * Använd **plats** att ange den geografiska plats där du vill köra jobbet.
   * Ange en ny eller befintlig **resursgruppen** för relaterade resurser för ditt program.
4. Välj **Skapa**.
Det kan ta några minuter för Stream Analytics-jobbet ska skapas. Du kan övervaka förloppet på meddelandehubben för att kontrollera status.
    
   ![Azure portal dataanalys bearbetar jobbet Skapa jobb](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Nytt jobb visas status för **Skapad**. Observera att den **starta** är inaktiverat. Konfigurera jobbet indata-, fråge- och utdata innan du startar jobbet.

   
   ![Azure portal dataanalys bearbetning jobbstatus](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

