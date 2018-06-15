---
title: Importera Azure logganalys data till Power BI | Microsoft Docs
description: Powerbi är en molnbaserad business analytics-tjänst från Microsoft som innehåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  Den här artikeln beskriver hur du konfigurerar och importera logganalys data till Power BI och konfigurera den att uppdateras automatiskt.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30236156"
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importera Azure logganalys data till Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är en molnbaserad business analytics-tjänst från Microsoft som innehåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  Du kan importera resultaten av en logganalys loggen till en Power BI-dataset så att du kan dra nytta av dess funktioner, till exempel att kombinera data från olika källor och dela rapporter på webben och mobila enheter.

## <a name="overview"></a>Översikt
Om du vill importera data från en logganalys-arbetsytan till Power BI, skapar du en datamängd i Power BI baserat på en logg sökfråga i logganalys.  Frågan körs varje gång dataset uppdateras.  Du kan sedan skapa Power BI-rapporter som använder data från datamängden.  För att skapa datauppsättningen i Power BI, exportera frågan från logganalys till [Power Query (M) språk](https://msdn.microsoft.com/library/mt807488.aspx).  Du sedan används för att skapa en fråga i Power BI Desktop och publicera det till Power BI som en datamängd.  Information för den här processen beskrivs nedan.

![Logganalys till Powerbi](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Exportera fråga
Börja med att skapa en [loggen Sök](log-analytics-log-search-new.md) som returnerar data från logganalys som du vill fylla i Power BI-datauppsättningen.  Sedan kan du exportera den frågan till [Power Query (M) språk](https://msdn.microsoft.com/library/mt807488.aspx) som kan användas av Power BI Desktop.

1. Skapa loggen sökningen i logganalys att extrahera data för datamängden.
2. Om du använder loggen Sök-portalen klickar du på **Power BI**.  Om du använder Analytics-portalen, Välj **exportera** > **Power BI fråga (M)**.  Båda dessa alternativ exportera frågan till en textfil med namnet **PowerBIQuery.txt**. 

    ![Exportera loggen Sök](media/log-analytics-powerbi/export-logsearch.png) ![Exportera loggen Sök](media/log-analytics-powerbi/export-analytics.png)

3. Öppna filen och kopiera innehållet.

## <a name="import-query-into-power-bi-desktop"></a>Importera frågan till Power BI Desktop
Power BI Desktop är ett skrivbordsprogram som hjälper dig att skapa datauppsättningar och rapporter som kan publiceras till Power BI.  Du kan också använda den för att skapa en fråga med Power Query-språket som exporterats från logganalys. 

1. Installera [Power BI Desktop](https://powerbi.microsoft.com/desktop/) om du inte redan har det och öppna programmet.
2. Välj **hämta Data** > **tom fråga** att öppna en ny fråga.  Välj sedan **avancerade Editor** och klistra in innehållet i den exporterade filen i frågan. Klicka på **Klar**.

    ![Power BI Desktop-fråga](media/log-analytics-powerbi/desktop-new-query.png)

5. Frågan körs, och resultatet visas.  Du kan uppmanas att autentiseringsuppgifter för att ansluta till Azure.  
6. Skriv ett beskrivande namn för frågan.  Standardvärdet är **Fråga1**. Klicka på **stängs och** att lägga till datamängden i rapporten.

    ![Power BI Desktop namn](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicera till Powerbi
När du publicerar till Power BI, skapas en datauppsättning och en rapport.  Om du skapar en rapport i Power BI Desktop kommer sedan detta att publiceras med dina data.  Om inte en tom rapport skapas.  Du kan ändra rapporten i Power BI eller skapa ett nytt baserat på datamängden.

8. Skapa en rapport baserad på dina data.  Använd [Power BI Desktop dokumentationen](https://docs.microsoft.com/power-bi/desktop-report-view) om du inte känner till den.  När du är redo att skicka den till Power BI klickar du på **publicera**.  När du uppmanas, Välj ett mål i Power BI-konto.  Om du inte har ett visst mål i åtanke, Använd **Min arbetsyta**.

    ![Publicera Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. När publiceringen är klar klickar du på **öppna i Power BI** att öppna Power BI med ditt nya dataset.


### <a name="configure-scheduled-refresh"></a>Konfigurera schemalagd uppdatering
Dataset som skapats i Power BI har samma data som du såg tidigare i Power BI Desktop.  Du måste uppdatera datauppsättningen regelbundet för att köra frågan igen och fylla det med den senaste informationen från logganalys.  

1. Klicka på arbetsytan där du har överfört rapporten och välj den **datauppsättningar** menyn. Välj snabbmenyn bredvid din ny datauppsättning och välj **inställningar**. Under **datakällans autentiseringsuppgifter** bör du ha ett meddelande att autentiseringsuppgifterna är ogiltiga.  Det beror på att du inte har angett autentiseringsuppgifterna för datamängden som används när den uppdaterar sina data.  Klicka på **redigera autentiseringsuppgifter** och ange autentiseringsuppgifter för åtkomst till logganalys.

    ![Power BI-schema](media/log-analytics-powerbi/powerbi-schedule.png)

5. Under **schemalagd uppdatering** aktiverar alternativet att **hålla data uppdaterade**.  Du kan ändra den **uppdateringsfrekvens** och en eller flera specifika gånger att köra uppdateringen.

    ![Power BI-uppdatering](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att skapa frågor som kan exporteras till Power BI.
* Lär dig mer om [Power BI](http://powerbi.microsoft.com) att skapa grafik baserad på logganalys exporter.
