---
title: Importera Azure Log Analytics-data till Power BI | Microsoft-dokument
description: Power BI är en molnbaserad företagsanalystjänst från Microsoft som tillhandahåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  I den här artikeln beskrivs hur du konfigurerar och importerar Logganalysdata till Power BI och konfigurerar dem för att uppdateras automatiskt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8ff24d508eb35c4f2a04c7d024254fa6f1875da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659293"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importera Azure Monitor-loggdata till Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är en molnbaserad företagsanalystjänst från Microsoft som tillhandahåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  Du kan importera resultatet av en Azure Monitor-loggfråga till en Power BI-datauppsättning så att du kan dra nytta av dess funktioner, till exempel kombinera data från olika källor och dela rapporter på webben och mobila enheter.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Översikt
Om du vill importera data från en [Log Analytics-arbetsyta](manage-access.md) i Azure Monitor till Power BI skapar du en datauppsättning i Power BI baserat på en [loggfråga](../log-query/log-query-overview.md) i Azure Monitor.  Frågan körs varje gång datauppsättningen uppdateras.  Du kan sedan skapa Power BI-rapporter som använder data från datauppsättningen.  Om du vill skapa datauppsättningen i Power BI exporterar du frågan från Log Analytics till [Power Query (M) språk](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification).  Du använder sedan detta för att skapa en fråga i Power BI Desktop och sedan publicera den till Power BI som en datauppsättning.  Detaljerna för denna process beskrivs nedan.

![Logga analytics till Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Exportera fråga
Börja med att skapa en [loggfråga](../log-query/log-query-overview.md) som returnerar de data som du vill fylla i Power BI-datauppsättningen.  Du exporterar sedan frågan till [Power Query (M) språk](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) som kan användas av Power BI Desktop.

1. [Skapa loggfrågan i Log Analytics](../log-query/get-started-portal.md) för att extrahera data för din datauppsättning.
2. Välj **Exportera** > **Power BI-fråga (M)**.  Detta exporterar frågan till en textfil som heter **PowerBIQuery.txt**. 

    ![Sök efter exportlogg](media/powerbi/export-analytics.png)

3. Öppna textfilen och kopiera dess innehåll.

## <a name="import-query-into-power-bi-desktop"></a>Importera fråga till Power BI Desktop
Power BI Desktop är ett skrivbordsprogram som gör att du kan skapa datauppsättningar och rapporter som kan publiceras i Power BI.  Du kan också använda den för att skapa en fråga med det Power Query-språk som exporteras från Azure Monitor. 

1. Installera [Power BI Desktop](https://powerbi.microsoft.com/desktop/) om du inte redan har det och öppna programmet.
2. Välj Hämta**tom fråga för** **data** > om du vill öppna en ny fråga.  Välj sedan **Avancerad redigerare** och klistra in innehållet i den exporterade filen i frågan. Klicka på **Klar**.

    ![Power BI-skrivbordsfråga](media/powerbi/desktop-new-query.png)

5. Frågan körs och dess resultat visas.  Du kan bli tillfrågad om autentiseringsuppgifter för att ansluta till Azure.  
6. Skriv in ett beskrivande namn för frågan.  Standard är **Query1**. Klicka på **Stäng och Använd** om du vill lägga till datauppsättningen i rapporten.

    ![Power BI-skrivbordsnamn](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicera till Power BI
När du publicerar till Power BI skapas en datauppsättning och en rapport.  Om du skapar en rapport i Power BI Desktop publiceras den med dina data.  Om inte, skapas en tom rapport.  Du kan ändra rapporten i Power BI eller skapa en ny baserat på datauppsättningen.

1. Skapa en rapport baserat på dina data.  Använd [Power BI Desktop-dokumentation](https://docs.microsoft.com/power-bi/desktop-report-view) om du inte är bekant med den.  
1. När du är redo att skicka den till Power BI klickar du på **Publicera**.  
1. När du uppmanas till det väljer du en destination i ditt Power BI-konto.  Om du inte har en specifik destination i åtanke använder du **Min arbetsyta**.

    ![Publicera Power BI Desktop](media/powerbi/desktop-publish.png)

1. När publiceringen är klar klickar du på **Öppna i Power BI** för att öppna Power BI med den nya datauppsättningen.


### <a name="configure-scheduled-refresh"></a>Konfigurera schemalagd uppdatering
Datauppsättningen som skapas i Power BI har samma data som du tidigare såg i Power BI Desktop.  Du måste uppdatera datauppsättningen regelbundet för att köra frågan igen och fylla den med de senaste data från Azure Monitor.  

1. Klicka på arbetsytan där du laddade upp rapporten och välj **menyn Datauppsättningar.** 
1. Välj snabbmenyn bredvid den nya datauppsättningen och välj **Inställningar**. 
1. Under **Autentiseringsuppgifter för datakälla** bör du ha ett meddelande om att autentiseringsuppgifterna är ogiltiga.  Detta beror på att du ännu inte har angett autentiseringsuppgifter för datauppsättningen som ska användas när den uppdaterar sina data.  
1. Klicka på **Redigera autentiseringsuppgifter** och ange autentiseringsuppgifter med åtkomst till Log Analytics-arbetsytan i Azure Monitor. Om du behöver tvåfaktorsautentisering väljer du **OAuth2** för **autentiseringsmetoden** som ska uppmanas att logga in med dina autentiseringsuppgifter.

    ![Power BI-schema](media/powerbi/powerbi-schedule.png)

5. Aktivera alternativet För att **hålla dina data uppdaterade**under **Schemalagd uppdatering** .  Du kan också ändra **uppdateringsfrekvensen** och en eller flera specifika tider för att köra uppdateringen.

    ![Power BI-uppdatering](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggsökningar](../log-query/log-query-overview.md) för att skapa frågor som kan exporteras till Power BI.
* Läs mer om [Power BI](https://powerbi.microsoft.com) för att skapa visualiseringar baserat på Azure Monitor-loggexporter.
