---
title: Exportera till Power BI från Azure Application Insights | Microsoft-dokument
description: Analytics-frågor kan visas i Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e17ca6e07ec76f0a7a1cb04f7aa13619fb9970c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664005"
---
# <a name="feed-power-bi-from-application-insights"></a>Feed Power BI från Application Insights
[Power BI](https://www.powerbi.com/) är en uppsättning affärsverktyg som hjälper dig att analysera data och dela insikter. Omfattande instrumentpaneler är tillgängliga på alla enheter. Du kan kombinera data från många källor, inklusive Analytics-frågor från [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Det finns tre metoder för att exportera Application Insights-data till Power BI:

* [**Exportera Analytics-frågor**](#export-analytics-queries). Detta är föredragen metod. Skriv vilken fråga du vill och exportera den till Power BI. Du kan placera den här frågan på en instrumentpanel tillsammans med andra data.
* [**Kontinuerlig export och Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Den här metoden är användbar om du vill lagra dina data under en längre tid. Om du inte har ett utökat datalagringskrav använder du frågasmetoden för exportanalys. Kontinuerlig export och Stream Analytics innebär mer arbete för att ställa in och ytterligare lagringsutrymme.
* **Ström BI-adapter**. Uppsättningen diagram är fördefinierad, men du kan lägga till egna frågor från andra källor.

> [!NOTE]
> Power BI-kortet är nu **inaktuellt**. De fördefinierade diagrammen för den här lösningen fylls i av statiska oederbara frågor. Du har inte möjlighet att redigera dessa frågor och beroende på vissa egenskaper för dina data är det möjligt för anslutningen till Power BI att lyckas, men inga data fylls i. Detta beror på uteslutningsvillkor som anges i den hårdkodade frågan. Även om den här lösningen fortfarande kan fungera för vissa kunder, är den rekommenderade lösningen på grund av att kortet inte är flexibel att använda [**funktionen exportera Analytics-fråga.**](#export-analytics-queries)

## <a name="export-analytics-queries"></a>Exportera Analytics-frågor
Med den här vägen kan du skriva alla Analytics-frågor som du vill eller exportera från användningskanaler och sedan exportera den till en Power BI-instrumentpanel. (Du kan lägga till instrumentpanelen som skapats av kortet.)

### <a name="one-time-install-power-bi-desktop"></a>En gång: installera Power BI Desktop
Om du vill importera frågan Application Insights använder du skrivbordsversionen av Power BI. Sedan kan du publicera den på webben eller till din Power BI-molnarbetsyta. 

Installera [Power BI-skrivbordet](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportera en Analytics-fråga
1. [Öppna Analytics och skriv frågan](../../azure-monitor/log-query/get-started-portal.md).
2. Testa och förfina frågan tills du är nöjd med resultatet. Kontrollera att frågan körs korrekt i Analytics innan du exporterar den.
3. Välj Power BI **(M)** på **Export-menyn** . Spara textfilen.
   
    ![Skärmbild av Analytics, med exportmenyn markerad](./media/export-power-bi/analytics-export-power-bi.png)
4. Välj Hämta**tom fråga för** **data** > i Power BI-skrivbordet . Välj sedan **Avancerad redigerare**under Visa under **Visa**.

    Klistra in det exporterade M-språkskriptet i den avancerade redigeraren.

    ![Skärmbild av Power BI Desktop, med avancerad redigerare markerad](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Om du vill att Power BI ska kunna komma åt Azure kan du behöva ange autentiseringsuppgifter. Använd **organisationskontot** för att logga in med ditt Microsoft-konto.
   
    ![Skärmbild av dialogrutan Inställningar för Power BI-frågor](./media/export-power-bi/power-bi-import-sign-in.png)

    Om du behöver verifiera autentiseringsuppgifterna använder du **menykommandot Inställningar för datakälla** i frågeredigeraren. Var noga med att ange de autentiseringsuppgifter du använder för Azure, som kan skilja sig från dina autentiseringsuppgifter för Power BI.
6. Välj en visualisering för frågan och välj fälten för x-axel- , y-axel och segmenteringsdimension.
   
    ![Skärmbild av visualiseringsalternativ för Power BI-skrivbordet](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publicera rapporten på power bi-molnarbetsytan. Därifrån kan du bädda in en synkroniserad version till andra webbsidor.
   
    ![Skärmbild av Power BI Desktop, med knappen Publicera markerad](./media/export-power-bi/publish-power-bi.png)
8. Uppdatera rapporten manuellt med jämna mellanrum eller ställ in en schemalagd uppdatering på alternativsidan.

### <a name="export-a-funnel"></a>Exportera en tratt
1. [Gör din tratt](../../azure-monitor/app/usage-funnels.md).
2. Välj **Power BI**.

   ![Skärmbild av Power BI-knappen](./media/export-power-bi/button.png)

3. Välj Hämta**tom fråga för** **data** > i Power BI-skrivbordet . Välj sedan **Avancerad redigerare**under Visa under **Visa**.

   ![Skärmbild av Power BI Desktop, med knappen Tom fråga markerad](./media/export-power-bi/blankquery.png)

   Klistra in det exporterade M-språkskriptet i den avancerade redigeraren. 

   ![Skärmbild av Power BI Desktop, med avancerad redigerare markerad](./media/export-power-bi/advancedquery.png)

4. Markera objekt från frågan och välj en visualisering av tratten.

   ![Skärmbild av visualiseringsalternativ för Power BI-skrivbordet](./media/export-power-bi/selectsequence.png)

5. Ändra titeln så att den blir meningsfull och publicera rapporten på power bi-molnarbetsytan. 

   ![Skärmbild av Power BI Desktop, med rubrikändring markerad](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Felsökning

Du kan stöta på fel som hör till autentiseringsuppgifter eller storleken på datauppsättningen. Här är lite information om vad du ska göra åt dessa fel.

### <a name="unauthorized-401-or-403"></a>Obehörig (401 eller 403)
Detta kan inträffa om uppdateringstoken inte har uppdaterats. Prova de här stegen för att se till att du fortfarande har åtkomst:

1. Logga in på Azure-portalen och se till att du kan komma åt resursen.
2. Försök att uppdatera autentiseringsuppgifterna för instrumentpanelen.
3. Försök att rensa cacheminnet från PowerBI-skrivbordet.


   Om du har tillgång till och uppdatera autentiseringsuppgifterna inte fungerar, vänligen öppna en supportbiljett.

### <a name="bad-gateway-502"></a>Dålig gateway (502)
Detta orsakas vanligtvis av en Analytics-fråga som returnerar för mycket data. Prova att använda ett mindre tidsintervall för frågan. 

Om du inte uppfyller dina krav om du minskar datauppsättningen som kommer från Analytics-frågan bör du överväga att använda [API:et](https://dev.applicationinsights.io/documentation/overview) för att hämta en större datauppsättning. Så här konverterar du M-Query-exporten för att använda API:et.

1. Skapa en [API-nyckel](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Uppdatera Power BI M-skriptet som du exporterade från Analytics genom att ersätta Azure Resource Manager-URL:en med API:et för Application Insights.
   * Ersätt **https:\//management.azure.com/subscriptions/...**
   * med, **https:\//api.applicationinsights.io/beta/apps/...**
3. Slutligen uppdaterar du autentiseringsuppgifterna till grundläggande och använder din API-nyckel.

**Befintligt skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Uppdaterat skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Om provtagning
Beroende på hur mycket data som skickas av ditt program kanske du vill använda funktionen för adaptiv sampling, som bara skickar en procentandel av din telemetri. Detsamma gäller om du manuellt har ställt in sampling antingen i SDK eller vid intag. [Läs mer om sampling](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI-adapter (föråldrad)
Med den här metoden skapas en komplett instrumentpanel med telemetri åt dig. Den ursprungliga datauppsättningen är fördefinierad, men du kan lägga till mer data i den.

### <a name="get-the-adapter"></a>Hämta adaptern
1. Logga in på [Power BI](https://app.powerbi.com/).
2. Öppna **Hämta data** ![skärmdump av GetData](./media/export-power-bi/001.png)Icon i nedre vänstra hörnet , **Tjänster**.

    ![Skärmbilder av datakällan Hämta från Application Insights](./media/export-power-bi/002.png)

3. Välj **Hämta det nu** under Application Insights.

   ![Skärmbilder av datakällan Hämta från Application Insights](./media/export-power-bi/003.png)
4. Ange information om din Application **Insights-resurs**och logga sedan in .

    ![Skärmbild av datakällan Hämta från Application Insights](./media/export-power-bi/005.png)

     Den här informationen finns i översiktsfönstret För programinsikter:

     ![Skärmbild av datakällan Hämta från Application Insights](./media/export-power-bi/004.png)

5. Öppna den nyligen skapade Application Insights Power BI App.

6. Vänta en minut eller två innan data importeras.

    ![Skärmbild av Power BI-adapter](./media/export-power-bi/010.png)

Du kan redigera instrumentpanelen genom att kombinera programstatistikdiagrammen med andra källor och med Analytics-frågor. Du kan hämta fler diagram i visualiseringsgalleriet och varje diagram har parametrar som du kan ange.

Efter den första importen fortsätter instrumentpanelen och rapporterna att uppdateras dagligen. Du kan styra uppdateringsschemat på datauppsättningen.

## <a name="next-steps"></a>Nästa steg
* [Power BI - Lär dig](https://www.powerbi.com/learning/)
* [Självstudiekurs för Analytics](../../azure-monitor/log-query/get-started-portal.md)

