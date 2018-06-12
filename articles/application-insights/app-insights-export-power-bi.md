---
title: Exportera till Powerbi från Azure Application Insights | Microsoft Docs
description: Analytics-frågor kan visas i Power BI.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: dee3313082fbe75d76bf27105979cf7e869fafad
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294130"
---
# <a name="feed-power-bi-from-application-insights"></a>Powerbi-feed från Application Insights
[Power BI](http://www.powerbi.com/) är en uppsättning verktyg för business som hjälper dig att analysera och dela information. Omfattande instrumentpaneler är tillgängliga på varje enhet. Du kan kombinera data från flera källor, inklusive Analytics-frågor från [Azure Application Insights](app-insights-overview.md).

Det finns tre metoder för att exportera Application Insights-data till Power BI. Du kan använda dem enskilt eller tillsammans.

* [**Power BI kortet**](#power-pi-adapter). Konfigurera en komplett instrumentpanel för telemetri från din app. Uppsättningen diagram är fördefinierade, men du kan lägga till egna frågor från andra källor.
* [**Exportera Analytics frågor**](#export-analytics-queries). Skriva frågan du vill exportera till Power BI. Du kan skriva en fråga med hjälp av Analytics eller skriva dem från Trattarna användning. Du kan placera den här frågan på en instrumentpanel, tillsammans med andra data.
* [**Den löpande exporten och Azure Stream Analytics**](app-insights-export-stream-analytics.md). Den här metoden är användbar om du vill behålla dina data under långa perioder. Om du inte att använda en av de andra metoderna eftersom den här innebär mer arbete för att ställa in.

## <a name="power-bi-adapter"></a>Power BI-kort
Den här metoden skapas en komplett instrumentpanel för telemetri. Den första datamängden är fördefinierade, men du kan lägga till mer data till den.

### <a name="get-the-adapter"></a>Hämta kortet
1. Logga in på [Power BI](https://app.powerbi.com/).
2. Öppna **hämta Data**, **Services**, och sedan **Programinsikter**.
   
    ![Skärmbilder för att hämta från Application Insights-datakälla](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Ange information om Application Insights-resurs.
   
    ![Skärmbild av hämta från Application Insights-datakälla](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Vänta en minut eller två för data som ska importeras.
   
    ![Skärmbild av Power BI-kort](./media/app-insights-export-power-bi/010.png)

Du kan redigera instrumentpanelen och kombinera Application Insights-diagram med de andra källor och med Analytics-frågor. Du kan få fler diagram i galleriet för visualisering och varje diagram har parametrar som du kan ange.

Efter importen fortsätter instrumentpanelen och rapporterna att uppdatera varje dag. Du kan styra uppdateringsschema för datamängden.

## <a name="export-analytics-queries"></a>Exportera Analytics-frågor
Den här vägen kan du skriva alla Analytics-fråga som du gillar eller exportera från användning skorstenar och sedan exportera som en Power BI-instrumentpanelen. (Du kan lägga till den instrumentpanel som har skapats av nätverkskort.)

### <a name="one-time-install-power-bi-desktop"></a>En gång: Installera Power BI Desktop
Om du vill importera Application Insights frågan kan du använda skrivbordsversionen av Power BI. Du kan sedan publicera den på webben eller på din arbetsyta för Power BI-molnet. 

Installera [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportera en Analytics-fråga
1. [Öppna Analytics och skriva en fråga](app-insights-analytics-tour.md).
2. Testa och förfina frågan tills du är nöjd med resultaten. Kontrollera att frågan körs korrekt i Analytics innan du exporterar den.
3. På den **exportera** -menyn, Välj **Power BI (M)**. Spara textfilen.
   
    ![Skärmbild av Analytics med menyn Exportera markerat](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Välj i Power BI Desktop **hämta Data** > **tom fråga**. I frågeredigeraren för under **visa**väljer **avancerade Editor**.

    Klistra in skriptet exporterade M språk i Redigeraren för avancerad.

    ![Skärmbild av Power BI Desktop, med Avancerad redigerare markerat](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Du kan behöva ange autentiseringsuppgifter så att Power BI åtkomst till Azure. Använd **organisationskonto** att logga in med ditt Microsoft-konto.
   
    ![Skärmbild av Power BI frågan dialogrutan Inställningar](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Om du behöver verifiera autentiseringsuppgifterna använder den **inställningar för datakälla** menykommandot i frågeredigeraren. Se till att ange de autentiseringsuppgifter som du använder för Azure, vilket kan skilja sig från dina autentiseringsuppgifter för Power BI.
2. Välj en visualisering för din fråga och välj fält för x-axeln, y-axeln och segmentera dimension.
   
    ![Skärmbild av Power BI Desktop vizualization alternativ](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publicera din rapporten till Power BI molnet arbetsytan. Du kan bädda in en synkroniserade version till andra webbsidor därifrån.
   
    ![Skärmbild av Power BI Desktop, med knappen Publicera markerat](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Uppdatera rapporten manuellt med intervall eller konfigurera en schemalagd uppdatering på alternativsidan.

### <a name="export-a-funnel"></a>Exportera en tratten
1. [Kontrollera din Trattens](usage-funnels.md).
2. Välj **Power BI**. 

   ![Skärmbild av Power BI-knappen](./media/app-insights-export-power-bi/button.png)
   
3. Välj i Power BI Desktop **hämta Data** > **tom fråga**. I frågeredigeraren för under **visa**väljer **avancerade Editor**.

   ![Skärmbild av Power BI Desktop, med tom fråga knappen markerad](./media/app-insights-export-power-bi/blankquery.png)

   Klistra in skriptet exporterade M språk i Redigeraren för avancerad. 

   ![Skärmbild av Power BI Desktop, med Avancerad redigerare markerat](./media/app-insights-export-power-bi/advancedquery.png)

4. Välj objekt från frågan och väljer en Trattens visualisering.

   ![Skärmbild av Power BI Desktop vizualization alternativ](./media/app-insights-export-power-bi/selectsequence.png)

5. Ändra så att den blir beskrivande rubrik och publicera rapporten till Power BI molnet arbetsytan. 

   ![Skärmbild av Power BI Desktop, med rubriken ändringen markerat](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Felsökning

Kan det uppstå fel som rör autentiseringsuppgifter eller storleken på datamängden. Här är viss information om hur du kan åtgärda dessa fel.

### <a name="unauthorized-401-or-403"></a>Obehörig (401 eller 403)
Detta kan inträffa om uppdateringstoken inte har uppdaterats. Gör så här om du vill kontrollera att du har fortfarande åtkomst:

1. Logga in på Azure-portalen och kontrollera att du har åtkomst till resursen.
2. Försök att uppdatera autentiseringsuppgifterna för instrumentpanelen.

 Om du har åtkomst och uppdatera autentiseringsuppgifterna inte fungerar, öppna ett supportärende.

### <a name="bad-gateway-502"></a>Ogiltig Gateway (502)
Detta orsakas normalt av en Analytics-fråga som returnerar för mycket data. Försök med ett mindre tidsintervall för frågan. 

Om du minskar datauppsättningen kommer från Analytics-fråga inte uppfyller dina krav kan du använda den [API](https://dev.applicationinsights.io/documentation/overview) och hämtar en större datamängd. Här är hur du konverterar exporten M-fråga om du vill använda API: et.

1. Skapa en [API-nyckeln](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Uppdatera Power BI M-skriptet som du exporterade från Analytics genom att ersätta Azure Resource Manager-URL med Application Insights API.
   * Ersätt  **https://management.azure.com/subscriptions/...**
   * med,  **https://api.applicationinsights.io/beta/apps/...**
3. Slutligen uppdatera autentiseringsuppgifterna till basic och Använd API-nyckel.
  

**Befintligt skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Uppdaterat skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Om provtagning
Om ditt program skickar stora mängder data, kanske du vill använda funktionen anpassningsbar provtagning som skickar bara en del av din telemetri. Detsamma gäller om du manuellt har angett samplingsfrekvensen i SDK eller på införandet. [Lär dig mer om sampling](app-insights-sampling.md).


## <a name="next-steps"></a>Nästa steg
* [Power BI - Läs](http://www.powerbi.com/learning/)
* [Analytics-självstudier](app-insights-analytics-tour.md)

