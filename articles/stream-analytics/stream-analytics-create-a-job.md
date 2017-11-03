---
title: "Skapa ett data analytics-jobb för bearbetning för Stream Analytics | Microsoft Docs"
description: "Skapa ett data analytics-jobb för bearbetning för Stream Analytics | Learning sökvägssegment."
keywords: analytics databearbetning
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 05fdf1e20efd129cdfc27e1d37bc9e124edf5dcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Skapa ett data analytics-jobb för bearbetning för Stream Analytics
Resursen på den översta nivån i Azure Stream Analytics är en Stream Analytics-jobbet.  Det består av en eller flera inkommande datakällor, en fråga uttrycka data transformation och ett eller flera mål för utdata som resultat har skrivits till. Tillsammans gör dessa att användaren kan utföra dataanalys bearbetning för strömmande datascenarier.

Om du vill börja använda Stream Analytics, börjar du med att skapa ett nytt Stream Analytics-jobb.  Observera att den här åtgärden har ingen fakturering effekter tills jobbet har startats.

1. Logga in på nätet [klassiska Azure-portalen](http://manage.windowsazure.com) eller [Azure-portalen](https://portal.azure.com/).
2. I portalen: **klickar du på ny**, klicka på **datatjänster** eller **dataanalys** beroende på din portal och klicka sedan på **Azure Stream Analytics** eller **Stream Analytics** och sedan **Snabbregistrering**.
   
   ![Guiden analytics databearbetning](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![Skapa dataanalys bearbetning jobbet](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Ange önskad konfiguration för Stream Analytics-jobbet.
   
   * I den **jobbnamn** anger du ett namn som identifierar Stream Analytics-jobbet. När den **jobbnamn** har verifierats, visas en grön kryssmarkering i jobbnamn. Den **jobbnamn** får bara innehålla alfanumeriska tecken och '-' tecken och måste vara mellan 3 och 63 tecken.
   * Använd **Region** i Azure-portalen eller **plats** i Azure portal för att ange den geografiska plats där du vill köra jobbet.
   * Om du använder Azure portal, Välj eller skapa ett lagringskonto som ska användas som den **Lagringskonto för Regional övervakning**. Det här lagringskontot används för att lagra övervakningsdata för alla Stream Analytics-jobb som körs i den här regionen.
   * Om använder Azure-portalen, ange en ny eller befintlig **resursgruppen** för relaterade resurser för ditt program.
4. När de nya alternativ för Stream Analytics-jobbet har konfigurerats, klickar du på **skapa Stream Analytics-jobbet**. Det kan ta några minuter för Stream Analytics-jobbet ska skapas. Du kan övervaka förloppet på meddelandehubben för att kontrollera status.
   
   ![Data analytics bearbetning jobbet meddelandehubben](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Azure portal dataanalys bearbetar jobbet Skapa jobb](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Nytt jobb visas status för **Skapad**. Observera att den **starta** är inaktiverat. Konfigurera jobbet indata-, fråge- och utdata innan du startar jobbet.
   
   ![Databearbetning analytics-jobbet Status](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Azure portal dataanalys bearbetning jobbstatus](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

