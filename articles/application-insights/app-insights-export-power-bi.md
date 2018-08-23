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
ms.date: 08/10/2018
ms.author: mbullwin
ms.openlocfilehash: fc651b3bc28e59c5c5a195211d811e206eee3e42
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42059719"
---
# <a name="feed-power-bi-from-application-insights"></a>Powerbi-flöde från Application Insights
[Power BI](http://www.powerbi.com/) är en uppsättning verktyg för business som hjälper dig att analysera data och dela insikter. Omfattande instrumentpaneler är tillgängliga på varje enhet. Du kan kombinera data från många källor, inklusive Analytics-frågor från [Azure Application Insights](app-insights-overview.md).

Det finns tre metoder för att exportera Application Insights-data till Power BI:

* [**Exportera analysfrågor**](#export-analytics-queries). Det här är den bästa metoden. Skriva någon fråga som du vill och exportera den till Power BI. Du kan placera den här frågan på en instrumentpanel, tillsammans med andra data.
* [**Löpande export och Azure Stream Analytics**](app-insights-export-stream-analytics.md). Den här metoden är användbar om du vill lagra dina data under långa tidsperioder. Om du inte har ett krav för kvarhållning av utökade data kan du använda metoden export analytics fråga. Löpande export och Stream Analytics omfattar mer arbete att ställa in och kostnader för ytterligare lagringsutrymme.
* [**Power BI nätverkskort**](#power-pi-adapter). Uppsättningen diagram är fördefinierad, men du kan lägga till egna frågor från andra källor.

> [!NOTE]
> Power BI-kortet är nu **inaktuell**. Fördefinierade diagram för den här lösningen är ifyllda av statiska inte går att redigera frågor. Du har inte möjlighet att redigera dessa frågor och beroende på vissa egenskaper hos dina data är det möjligt för anslutning till Power BI ska lyckas, men inga data har fyllts i. Detta beror på exkluderingsvillkor som anges i hårdkodad frågan. Även om den här lösningen kan fortfarande fungera för vissa kunder, på grund av bristande flexibilitet kortets den rekommenderade lösningen är att använda den [ **exportera analysfråga** ](#export-analytics-queries) funktioner.

## <a name="export-analytics-queries"></a>Exportera analysfrågor
Den här vägen kan du skriva eventuella Analytics-fråga som du gillar och exportera från användning Trattar och sedan exportera som på en Power BI-instrumentpanel. (Du kan lägga till på instrumentpanelen som skapats av kort.)

### <a name="one-time-install-power-bi-desktop"></a>En gång: Installera Power BI Desktop
För att importera din Application Insights-fråga måste använda du skrivbordsversionen av Power BI. Du kan sedan publicera den på webben eller på din arbetsyta för Power BI-molnet. 

Installera [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportera en Analytics-fråga
1. [Öppna Analytics och Skriv din fråga](app-insights-analytics-tour.md).
2. Testa och förfina frågan tills du är nöjd med resultaten. Kontrollera att frågan fungerar korrekt i Analytics innan du exporterar den.
3. På den **exportera** menyn, Välj **Power BI (M)**. Spara filen.
   
    ![Skärmbild av analyser med menyn Exportera markerat](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. I Power BI Desktop, Välj **hämta Data** > **tom fråga**. I frågeredigeraren under **visa**väljer **Avancerad redigerare**.

    Klistra in den exporterade M språkskript i Avancerad redigerare.

    ![Skärmbild av Power BI Desktop med Avancerad redigerare markerat](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Du kan behöva ange autentiseringsuppgifter så att Power BI för att få åtkomst till Azure. Använd **organisationskonto** att logga in med ditt Microsoft-konto.
   
    ![Skärmbild av Power BI frågeinställningar dialogrutan](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Om du vill verifiera autentiseringsuppgifterna kan använda den **Datakällsinställningar** menykommandot i frågeredigeraren. Glöm inte att ange de autentiseringsuppgifter som du använder för Azure, vilket kan skilja sig från dina autentiseringsuppgifter för Power BI.
2. Välj en visualisering för din fråga och välj fält för x-axeln, y-axeln och segmentera dimension.
   
    ![Skärmbild av Power BI Desktop visualisering alternativ](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publicera rapporten till din arbetsyta för Power BI-molnet. Därifrån kan du bädda in en synkroniserade version i andra webbsidor.
   
    ![Skärmbild av Power BI Desktop med knappen Publicera markerat](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Uppdatera rapporten manuellt med intervall, eller ställa in en schemalagd uppdatering på alternativsidan.

### <a name="export-a-funnel"></a>Exportera en tratt
1. [Gör din tratt](usage-funnels.md).
2. Välj **Power BI**.

   ![Skärmbild av Power BI-knappen](./media/app-insights-export-power-bi/button.png)

3. I Power BI Desktop, Välj **hämta Data** > **tom fråga**. I frågeredigeraren under **visa**väljer **Avancerad redigerare**.

   ![Skärmbild av Power BI Desktop med tom fråga knappen markerad](./media/app-insights-export-power-bi/blankquery.png)

   Klistra in den exporterade M språkskript i Avancerad redigerare. 

   ![Skärmbild av Power BI Desktop med Avancerad redigerare markerat](./media/app-insights-export-power-bi/advancedquery.png)

4. Välj objekt från frågan och välj en tratt-visualisering.

   ![Skärmbild av Power BI Desktop visualisering alternativ](./media/app-insights-export-power-bi/selectsequence.png)

5. Ändra rubriken så att den blir meningsfulla och publicerar rapporten till din arbetsyta för Power BI-molnet. 

   ![Skärmbild av Power BI Desktop, rubrik ändring av markerat](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Felsökning

Du kan stöta på fel som hör till autentiseringsuppgifter eller storleken på datauppsättningen. Här är lite information om hur du kan åtgärda dessa fel.

### <a name="unauthorized-401-or-403"></a>Obehörig (401 eller 403)
Detta kan inträffa om din uppdateringstoken inte har uppdaterats. Prova de här stegen för att säkerställa att du har fortfarande åtkomst:

1. Logga in på Azure portal och kontrollera att du kan komma åt resursen.
2. Försök att uppdatera autentiseringsuppgifterna för instrumentpanelen.

 Om du har åtkomst och uppdaterar autentiseringsuppgifterna som inte fungerar, öppnar du ett supportärende.

### <a name="bad-gateway-502"></a>Felaktig Gateway (502)
Detta orsakas normalt av en Analytics-fråga som returnerar för mycket data. Försök med ett mindre tidsintervall för frågan. 

Om du minskar den datauppsättning som kommer från analysfråga som inte uppfyller dina krav kan du använda den [API](https://dev.applicationinsights.io/documentation/overview) att hämta en större datauppsättning. Här är hur du konverterar M-Query-export för att använda API: et.

1. Skapa en [API-nyckel](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Uppdatera Power BI-M-skriptet som du exporterade från Analytics genom att ersätta URL: en för Azure Resource Manager med Application Insights API.
   * Ersätt  **https://management.azure.com/subscriptions/...**
   * med,  **https://api.applicationinsights.io/beta/apps/...**
3. Slutligen uppdatera autentiseringsuppgifterna till basic och Använd din API-nyckel.
  

**Befintligt skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Uppdaterade skriptet**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Om sampling
Om ditt program skickar stora mängder data, kan du använda funktionen Adaptiv sampling som skickar bara en del av din telemetri. Detsamma gäller om du har ställt in manuellt sampling i SDK: N eller på inmatning. [Läs mer om sampling](app-insights-sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI-kort (inaktuell)
Den här metoden skapar en fullständig telemetri-instrumentpanel. Den initiala datauppsättningen är fördefinierad, men du kan lägga till mer data till den.

### <a name="get-the-adapter"></a>Hämta nätverkskortet
1. Logga in på [Power BI](https://app.powerbi.com/).
2. Öppna **hämta Data** ![Skärmbild av GetData ikon i nedre vänstra hörnet](./media/app-insights-export-power-bi/001.png), **Services**.

    ![Skärmbilder av hämtar från Application Insights-datakälla](./media/app-insights-export-power-bi/002.png)

3. Välj **Hämta nu** under Application Insights.

   ![Skärmbilder av hämtar från Application Insights-datakälla](./media/app-insights-export-power-bi/003.png)
4. Ange uppgifter om Application Insights-resursen, och sedan **inloggning**.

    ![Skärmbild av hämtar från Application Insights-datakälla](./media/app-insights-export-power-bi/005.png)

     Den här informationen finns i översikt över Application Insights-fönstret:

     ![Skärmbild av hämtar från Application Insights-datakälla](./media/app-insights-export-power-bi/004.png)

5. Öppna den nyligen skapade Application Insights Power BI-appen.

6. Vänta en minut eller två innan data som ska importeras.

    ![Skärmbild av Power BI-kort](./media/app-insights-export-power-bi/010.png)

Du kan redigera instrumentpanelen, kombinera Application Insights-diagram med de andra källor och analysfrågor. Du kan få fler diagram i galleriet visualisering och varje diagram har parametrar som du kan ange.

Efter den första importen fortsätter instrumentpanelen och rapporterna att uppdateras dagligen. Du kan styra uppdateringsschemat för datauppsättningen.

## <a name="next-steps"></a>Nästa steg
* [Power BI – Lär dig](http://www.powerbi.com/learning/)
* [Analytics-självstudier](app-insights-analytics-tour.md)

