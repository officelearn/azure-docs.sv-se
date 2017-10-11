---
title: "Exportera till Powerbi från Application Insights | Microsoft Docs"
description: "Analytics-frågor kan visas i Power BI."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: bwren
ms.openlocfilehash: 350a65b1c6432baf258e014c9e63133d2b29e34f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Powerbi-feed från Application Insights
[Power BI](http://www.powerbi.com/) är en uppsättning verktyg för business analytics som hjälper dig att analysera data och dela information. Omfattande instrumentpaneler är tillgängliga på varje enhet. Du kan kombinera data från flera källor, inklusive Analytics-frågor från [Azure Application Insights](app-insights-overview.md).

Det finns tre metoder för att exportera Application Insights-data till Power BI. Du kan använda dem enskilt eller tillsammans.

* [**Power BI kortet** ](#power-pi-adapter) -Ställ in en komplett instrumentpanel för telemetri från din app. Uppsättningen diagram är fördefinierade, men du kan lägga till egna frågor från andra källor.
* [**Exportera Analytics frågor** ](#export-analytics-queries) -skriva en fråga som du vill med hjälp av Analytics och exportera dem till Power BI. Du kan placera den här frågan på en instrumentpanel tillsammans med andra data.
* [**Den löpande exporten och Stream Analytics** ](app-insights-export-stream-analytics.md) -detta innebär mer arbete för att ställa in. Det är användbart om du vill behålla dina data under långa perioder. I annat fall rekommenderas de andra metoderna.

## <a name="power-bi-adapter"></a>Power BI-kort
Den här metoden skapas en komplett instrumentpanel för telemetri. Den första datamängden är fördefinierade, men du kan lägga till mer data till den.

### <a name="get-the-adapter"></a>Hämta kortet
1. Logga in på [Power BI](https://app.powerbi.com/).
2. Öppna **hämta Data**, **Services**, **Application Insights**
   
    ![Hämta från Application Insights-datakälla](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Ange information om Application Insights-resurs.
   
    ![Hämta från Application Insights-datakälla](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Vänta en minut eller två för data som ska importeras.
   
    ![Power BI-kort](./media/app-insights-export-power-bi/010.png)

Du kan redigera instrumentpanelen och kombinera Application Insights-diagram med de andra källor och med Analytics-frågor. Det finns ett visualiseringen galleri där du kan få fler diagram och varje diagram har en parametrar som du kan ange.

Efter importen fortsätter instrumentpanelen och rapporterna att uppdatera varje dag. Du kan styra uppdateringsschema för datamängden.

## <a name="export-analytics-queries"></a>Exportera Analytics-frågor
Den här vägen kan du skriva alla Analytics-fråga som du vill och sedan exportera som en Power BI-instrumentpanelen. (Du kan lägga till den instrumentpanel som har skapats av nätverkskort.)

### <a name="one-time-install-power-bi-desktop"></a>En gång: Installera Power BI Desktop
Om du vill importera Application Insights frågan kan du använda skrivbordsversionen av Power BI. Men sedan du kan publicera den på webben eller på din arbetsyta för Power BI-molnet. 

Installera [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportera en Analytics-fråga
1. [Öppna Analytics och skriva en fråga](app-insights-analytics-tour.md).
2. Testa och förfina frågan tills du är nöjd med resultaten.

   **Kontrollera att frågan körs korrekt i Analytics innan du exporterar den.**
3. På den **exportera** -menyn, Välj **Power BI (M)**. Spara textfilen.
   
    ![Exportera Power BI-fråga](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. I Power BI Desktop select **hämta Data, tom fråga** och sedan i frågeredigeraren för under **visa** Välj **avancerade frågeredigeraren**.

    Klistra in skriptet exporterade M språk i avancerade frågeredigeraren.

    ![Avancerade frågeredigeraren](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Du kan behöva ange autentiseringsuppgifter för att låta Power BI åtkomst till Azure. Använda 'organisationskonto' att logga in med ditt Microsoft-konto.
   
    ![Ange autentiseringsuppgifter för Azure om du vill aktivera Power BI att köra frågan Application Insights](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Om du behöver verifiera autentiseringsuppgifterna kommandot inställningar för datakälla-menyn i frågeredigeraren. Var noga med för att ange de autentiseringsuppgifter som du använder för Azure, vilket kan skilja sig från dina autentiseringsuppgifter för Powerbi.)
2. Välj en visualisering för din fråga och välj fält för x-axeln, y-axeln och segmentera dimension.
   
    ![Välj visualiseringen](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publicera din rapporten till Power BI molnet arbetsytan. Du kan bädda in en synkroniserade version till andra webbsidor därifrån.
   
    ![Välj visualiseringen](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Uppdatera rapporten manuellt med intervall eller konfigurera en schemalagd uppdatering på alternativsidan.

## <a name="troubleshooting"></a>Felsökning

### <a name="401-or-403-unauthorized"></a>401 eller 403 obehörig 
Detta kan inträffa om refesh-token inte har uppdaterats. Gör så här för att säkerställa att du har åtkomst. Om du har åtkomst till och refershing autentiseringsuppgifterna inte fungerar, öppna ett supportärende.

1. Logga in på Azure-portalen och kontrollera att du har åtkomst till resursen
2. Försök att uppdatera autentiseringsuppgifterna för instrumentpanelen

### <a name="502-bad-gateway"></a>502 felaktig Gateway
Detta orsakas normalt av en Analytics-fråga som returnerar för mycket data. Du bör försöka använda en mindre tidsintervall eller genom att använda den [sedan](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#ago) eller [startofweek/startofmonth](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#startofweek) fungerar endast [projekt](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#project-operator) fält som du behöver.

Om att minska datauppsättningen kommer från Analytics-fråga inte uppfyller dina krav bör du använda den [API](https://dev.applicationinsights.io/documentation/overview) och hämtar en större datamängd. Här följer instruktioner om hur du konverterar M-Query exporten att använda API: et.

1. Skapa en [API-nyckel](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)
2. Uppdatera Power BI M-skriptet som du exporterade från Analytics genom att ersätta ARM-URL med AI-API (se exemplet nedan)
   * Ersätt **https://management.azure.com/subscriptions/...**
   * med, **https://api.applicationinsights.io/beta/apps/...**
3. Slutligen uppdatera autentiseringsuppgifter till grundläggande och Använd API-nyckel
  

**Befintligt skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Uppdaterat skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Om provtagning
Om ditt program skickar stora mängder data, kan funktionen för anpassningsbar provtagning fungerar och skicka endast en del av din telemetri. Detsamma gäller om du manuellt har angett samplingsfrekvensen i SDK eller på införandet. [Läs mer om sampling.](app-insights-sampling.md)


## <a name="next-steps"></a>Nästa steg
* [Power BI - Läs](http://www.powerbi.com/learning/)
* [Analytics-självstudier](app-insights-analytics-tour.md)

