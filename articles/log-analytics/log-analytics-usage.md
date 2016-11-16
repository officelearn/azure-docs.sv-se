---
title: "Analysera dataanvändning i Log Analytics| Microsoft Docs"
description: "Du kan använda Användningssidan i Log Analytics för att se hur mycket data som skickas till OMS-tjänsten."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/11/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4c1d0319e768eec69718d8b49db30069ec1331d9


---
# <a name="analyze-data-usage-in-log-analytics"></a>Analysera dataanvändning i Log Analytics
Log Analytics i Operations Management Suite (OMS) samlar in data och skickar det regelbundet till OMS-tjänsten.  Du kan använda sidan **Användning** för att se hur mycket data som skickas till OMS-tjänsten. Sidan **Användning** visar även hur mycket data som skickas varje dag av lösningar och hur ofta dina servrarna skickar data.

> [!NOTE]
> Om du har ett kostnadsfritt konto som har skapats med [OMS-webbplatsen](http://www.microsoft.com/oms) är du begränsad till att skicka 500 MB data till OMS-tjänsten dagligen. Om du når den dagliga begränsningen stoppar dataanalysen och återupptas i början av nästa dag. Du måste också skicka alla data som inte godkänns eller bearbetas av OMS.
> 
> 

Du kan visa din användning med hjälp av ikonen **Användning** på instrumentpanelen **Översikt** i OMS.

![användningsikon](./media/log-analytics-usage/usage-tile.png)

Om du har överskridit den dagliga användningsbegränsningen eller om du ligger nära gränsen kan du ta bort en lösning för att reducera den mängd data som du skickar till OMS-tjänsten. Mer information om att ta bort lösningar finns i [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md).

![instrumentpanelen användning](./media/log-analytics-usage/usage-dashboard.png)

På sidan **Användning** visas följande information:

* Genomsnittlig användning per dag
* Dataanvändning för varje lösningen under de senaste 30 dagarna
* Hur mycket dataservrarna i din miljö har skickat till OMS-tjänsten under de senaste 30 dagarna
* Dina dataplans prisnivå och uppskattade kostnad
* Information om dina servicenivåavtal (SLA), inklusive hur lång tid det tar för OMS att bearbeta dina data

## <a name="to-work-with-usage-data"></a>Att arbeta med användningsdata
1. På sidan **Översikt** klickar du på ikonen **Användning**.
2. På sidan **Användning** visas användningskategorier som visar områden du bryr dig om.
3. Om du har en lösning som förbrukar alltför mycket av din dagliga överföringskvot kan du ta bort lösningen.

## <a name="to-view-your-estimated-cost-and-billing-information"></a>Visa dina beräknade kostnad och faktureringsinformation
1. På sidan **Översikt** klickar du på ikonen **Användning**.
2. På sidan **Användning** under **Användning** klickar du på sparren (**>**) bredvid **Uppskattad kostnad**.
3. I den utökade informationen**Din dataplan** kan du se din uppskattade månadskostnad.  
    ![Din dataplan](./media/log-analytics-usage/usage-data-plan.png)
4. Om du vill se din faktureringsinformation klickar du på **Visa min faktura** för att visa prenumerationsinformation.
   * På prenumerationssidan klickar du på din prenumeration för att visa information och en radartikellista över användning.  
       ![prenumeration](./media/log-analytics-usage/usage-sub01.png)
   * På sidan Sammanfattning för din prenumeration kan du utföra en mängd åtgärder för att hantera och visa mer information om ditt abonnemang.  
       ![prenumerationsinformation](./media/log-analytics-usage/usage-sub02.png)

## <a name="to-view-data-batches-for-your-sla"></a>Visa databatchar för ditt SLA
1. På sidan **Översikt** klickar du på ikonen **Användning**.
2. Under **Servicenivåavtal** klickar du på **Hämta SLA-information**.
3. En Excel-XLSX-fil hämtas för granskning.  
    ![SLA-information](./media/log-analytics-usage/usage-sla-details.png)

## <a name="next-steps"></a>Nästa steg
* Bekanta dig med [Loggsökningar i Log Analytics](log-analytics-log-searches.md) för att visa detaljerad information som samlas in av olika lösningar.




<!--HONumber=Nov16_HO2-->


