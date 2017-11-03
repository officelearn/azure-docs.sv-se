---
title: "Felsöka med hjälp av åtgärden och tjänsten loggar i Stream Analytics | Microsoft Docs"
description: "Användningsinstruktioner Stream Analytics åtgärdsloggar"
keywords: "tjänstloggar"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: a2ed9676-f0bd-4398-87c8-a592779ac728
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 2ee0871752dc2a3da345339fb826340d44ae48d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Felsöka Stream Analytics-jobb med hjälp av loggar och drift
Alla Azure-tjänster ange operativa meddelandeloggning för användarna att registrera information som rör hanteringsåtgärder. Den här informationen kan användas för felsökning, till exempel visa jobbstatus jobb pågår i Azure Stream Analytics och felmeddelanden för att spåra förloppet för ett jobb med tiden från börjar bearbetningen till utdata.

## <a name="find-operation-logs-in-the-azure-portal"></a>Hitta åtgärden loggar i Azure-portalen
Åtgärdsloggar kan nås på två sätt:  

* Instrumentpanelen i Stream Analytics-jobbet  
* Tjänster i den klassiska Azure-portalen  

## <a name="dashboard-of-the-stream-analytics-job"></a>Instrumentpanelen i Stream Analytics-jobbet
En länk till en Stream Analytics-jobbet motsvarande loggarna visas på fliken instrumentpanelen i jobbets. Om du klickar på länken anger filtren så att den visar senaste loggar för det specifika jobbet.

  ![Välj Management Services-loggar](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Hanteringstjänster
Navigera till åtgärden-loggar manuellt för Stream Analytics och andra tjänster i den klassiska Azure-portalen:

1. Klicka på **hanteringstjänster** i den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj **Stream Analytics** för **typen** och namnet på jobbet för **tjänstnamnet**.  
   
   ![Välj Stream Analytics](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Hitta granskningsloggar i Azure-portalen
Om du vill hitta operativa loggar för Stream Analytics-jobbet i Azure-portalen klickar du på **Bläddra** och välj sedan **granskningsloggar**.

  ![Azure portal väljer Stream Analytics](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Då öppnas en skärm som visar händelser från de senaste sju dagarna för alla resurser i din prenumeration.  Du kan filtrera om du vill se händelser med en ange typ eller tidsintervall genom att klicka på den **Filter** kommando.

  ![Azure portal väljer Stream Analytics](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Hämta logginformation om
Du kan filtrera efter tidsintervall och Status för att visa loggar för jobbet.

I Azure-portalen klickar du på den **information** längst ned i fönstret för att visa mer information om en markerad händelse. 

  ![Välj information](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Klicka på en loggpost för att se de detaljerade händelserna i den i Azure-portalen.

  ![Azure portal väljer information](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

Därifrån kan du öppna den **detaljer** visa genom att klicka på händelsen.

  ![Azure portal väljer information](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Felsöka ett jobb som misslyckades
Klicka på sökikonen och typ ”misslyckad” i Azure-portalen. Detta ger ett resultat av alla loggar med fel. 

  ![Felsökning av ett jobb som misslyckades](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

I Azure-portalen kan du filtrera efter nivå av meddelandet för att visa **kritisk** händelser.

  ![Felsökning i Azure portal](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Du kan välja en av felen och klicka på den **information** mer information om felet.  Vissa felmeddelanden ger också information om hur du kan undvika problemet. 

  ![Åtgärdsinformation](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Om du behöver kontakta [stöd](https://azure.microsoft.com/support/options/) eller ange information i teamet via den [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), Observera driftsinformation, särskilt de **Korrelations-ID**. 

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

