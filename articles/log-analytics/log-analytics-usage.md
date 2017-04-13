---
title: "Analysera dataanvändning i Log Analytics| Microsoft Docs"
description: "Du kan använda instrumentpanelen Användning i Log Analytics för att se hur mycket data som skickas till OMS-tjänsten."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 7e3d4b83fefdc70f292cf85b682cf8ed756bf4c5
ms.openlocfilehash: e7f04df679604f274c8ad9bf4daddc63c8b5418a
ms.lasthandoff: 02/22/2017


---
# <a name="analyze-data-usage-in-log-analytics"></a>Analysera dataanvändning i Log Analytics
Log Analytics samlar in data och skickar dem regelbundet till OMS-tjänsten.  Du kan använda instrumentpanelen **Log Analytics Usage** (Log Analytics-användning) för att se hur mycket data som skickas till OMS-tjänsten. Instrumentpanelen visar också hur mycket data som skickas av lösningar och hur ofta servrarna skickar data.

> [!NOTE]
> Om du har ett kostnadsfritt konto kan du skicka högst 500 MB data per dag till OMS-tjänsten. Om du når den dagliga gränsen stoppas dataanalysen och återupptas i början av nästa dag. I så fall måste du skicka om data som inte accepterades eller bearbetades av OMS.

Om du överskrider eller är nära den dagliga användningsgränsen kan du överväga att ta bort en lösning för att minska mängden data som skickas till OMS-tjänsten. Mer information om att ta bort lösningar finns i [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md).

![instrumentpanelen användning](./media/log-analytics-usage/usage-dashboard01.png)

Instrumentpanelen **Log Analytics usage** (Log Analytics-användning) innehåller följande information:

- Datavolym
    - Datavolymen över tid (baserat på ditt aktuella tidsomfång)
    - Datavolym per lösning
    - Data som inte är associerade med en dator
- Datorer
    - Datorer som skickar data
    - Datorer utan data de senaste 24 timmarna
- Erbjudanden
    - Insikts- och analysnoder
    - Automatiserings- och styrningsnoder
    - Säkerhetsnoder
- Prestanda
    - Hur lång tid det tar att samla in och indexera data
- Lista med frågor

## <a name="understanding-nodes-for-oms-offers"></a>Förstå noder för OMS-erbjudanden

Om du har prisnivån *per nod (OMS)* debiteras du därefter baserat på antalet noder och lösningar som du har aktiverat. Du kan se hur många noder i varje erbjudande som används i avsnittet *erbjudanden* på instrumentpanelen för användning.

![instrumentpanelen användning](./media/log-analytics-usage/log-analytics-usage-offerings.png)

## <a name="to-work-with-usage-data"></a>Att arbeta med användningsdata
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com) med din Azure-prenumeration.
2. På **navmenyn** klickar du på **Fler tjänster** och skriver **Log Analytics** i listan med resurser. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Log Analytics**.  
    ![Azure-hubb](./media/log-analytics-usage/hub.png)
3. Instrumentpanelen för **Log Analytics** visar en lista över dina arbetsytor. Välj en arbetsyta.
4. På instrumentpanelen för *arbetsytan* klickar du på **Log Analytics usage** (Log Analytics-användning).
5. På instrumentpanelen **Log Analytics Usage** (Log Analytics-användning) klickar du på **Tid: Senaste 24 timmarna** om du vill ändra tidsintervallet.  
    ![tidsintervall](./media/log-analytics-usage/time.png)
6. Öppna de blad för användningskategori som visar de områden som du är intresserad av. Välj ett blad och klicka sedan på ett objekt på det om du vill visa mer information i [Loggsökning](log-analytics-log-searches.md).  
    ![användningsblad med exempeldata](./media/log-analytics-usage/blade.png)
7. Granska resultaten som returneras från sökningen på instrumentpanelen Loggsökning.  
    ![exempel på loggsökning för användning](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>Nästa steg
* Detaljerad information om data som samlas in och skickas till OMS av tjänster och lösningar finns i [Log searches in Log Analytics](log-analytics-log-searches.md) (Loggsökningar i Log Analytics).

