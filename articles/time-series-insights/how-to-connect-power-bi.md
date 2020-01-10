---
title: Anslut din miljö till Power BI-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du ansluter Azure Time Series Insights till Power BI för att dela, rita och visa data i hela organisationen.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 1f6b753052b882b75884503f8c05f34b8cead821
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749378"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualisera data från Time Series Insights i Power BI

Azure Time Series Insights är en plattform för att lagra, hantera, fråga och visualisera Time Series-data i molnet. [Power BI](https://powerbi.microsoft.com) är ett företags analys verktyg med omfattande visualiserings funktioner som gör att du kan dela insikter och resultat i hela organisationen. Båda tjänsterna kan nu integreras för att få det bästa av både Time Series Insights "inbyggda visualiserings funktioner" och Power BI.

Du lär dig följande:

* Ansluta Time Series Insights till Power BI med hjälp av Cloud Connector
* Skapa visuella objekt med dina data i Power BI
* Publicera rapporten för att Power BI och dela den med resten av organisationen

I slutet får du lära dig hur du visualiserar Time Series-data via Azure Time Series Insights och förbättrar den med data visualiseringen stark och enkel delning av Power BI.

Se till att du registrerar dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Krav

* Hämta och installera den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Ha eller skapa en [instans av Azure Time Series Insights för hands version](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Power BI Connector stöds för närvarande i Time Series Insights för hands versions förhands gransknings miljöer *som* har kon figurer ATS för **varmt lagring**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Anslut data från Time Series Insights till Power BI

Följ dessa steg om du vill ansluta din Time Series Insights-miljö till Power BI:

1. Öppna Time Series Insights Explorer
1. Exportera data som en fråga eller som rå data
1. Öppna Power BI Desktop
1. Läs in från anpassad fråga

### <a name="export-data-into-power-bi-desktop"></a>Exportera data till Power BI Desktop

Så här kommer du igång:

1. Öppna Time Series Insights Preview Explorer och granska dina data.
1. När du har skapat en vy som du är nöjd med, navigerar du till List menyn **fler åtgärder** och klickar på **Anslut till Power BI**.

    [Exportera ![Time Series Insights för hands versions Utforskaren](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Ange parametrarna i den här fliken:

   1. Ange en relativ tidsram att visa. Om du är nöjd med din befintliga vy lämnar du den som en **befintlig tidsram**.
   
   1. Välj mellan **aggregerade** och **råa händelser**. 
   
       > [!NOTE]
       > Du kan alltid aggregera dina data senare i Power BI, men du kan inte återgå till rå data efter AGG regering. 
       
       > [!NOTE]
       > Det finns en gräns för antal 100-K-händelser för rå data på händelse nivå.

       [![ansluta](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Om du inte har konfigurerat Time Series Insights-instansen för **varm lagring**visas en varning.

       [varning om ![varm lagring](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Du kan konfigurera din befintliga instans för **varm lagring** i Azure Portal.

1. Välj **Kopiera fråga till Urklipp**.
1. Starta Power BI Desktop nu.
1. I Power BI Desktop på fliken **Start** väljer du **Hämta data** i det övre vänstra hörnet och sedan **mer**.

    [List rutan ![hem](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Sök efter **Time Series Insights**, Välj **Azure Time Series Insights (beta)** och **Anslut**.

    [![ansluta Power BI till Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Du kan också navigera till fliken **Azure** , välja **Azure Time Series Insights (beta)** och sedan **ansluta**.
    
1. Dialog rutan meddelande visas med frågan om behörighet att ansluta till resurser från tredje part. Välj **Fortsätt**.

    [![välja Skapa anpassad fråga](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. I list menyn under **data källa**väljer du **Skapa anpassad fråga**. Klistra in från Urklipp i det valfria fältet **anpassad fråga (valfritt)** nedan och tryck sedan på **OK**.

    [![pass i den anpassade frågan och välj OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Data tabellen kommer nu att läsas in. Tryck på **load** för att läsa in i Power BI.

    [![granska inlästa data i tabellen och välj load](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Om du har slutfört de här stegen kan du gå vidare till nästa avsnitt.

## <a name="create-a-report-with-visuals"></a>Skapa en rapport med visuella objekt

Nu när du har importerat data till Power BI är det dags att bygga en rapport med visuella objekt.

1. Kontrol lera att du har valt **rapportvyn** på vänster sida av fönstret.

    [![Välj rapportvyn](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Välj önskat visualiserings alternativ i kolumnen **visualiseringar** . Välj till exempel **linje diagram**. Ett tomt linje diagram läggs till på din arbets yta.
 
1.  I listan **fält** väljer du **tidsstämpel** och drar den till fältet **axel** för att visa objekt längs X-axeln.

1.  Återigen, i listan **fält** , väljer du **TimeSeriesId** och drar den till fältet **värden** för att visa objekt utmed Y-axeln.

    [![skapa ett linje diagram](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Om du vill lägga till ett annat diagram på arbets ytan klickar du var som helst på arbets ytan utanför linje diagrammet och upprepar den här processen.

    [![skapa ytterligare diagram som ska delas](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

När du har skapat rapporten kan du publicera den till Power BI repor ting Services.

## <a name="advanced-editing"></a>Avancerad redigering

Om du redan har läst in en data uppsättning i Power BI men vill ändra frågan (till exempel parametrarna för datum/tid eller miljö-ID), kan du göra detta via Avancerad redigerare-funktionerna i Power BI. Läs mer i [Power BI-dokumentationen](https://docs.microsoft.com/power-bi/desktop-query-overview) .

Som en översikt:

1. I Power BI Desktop väljer du **Redigera frågor**.
1. Tryck på **avancerad redigerare**.

    [![redigera frågor i Avancerad redigerare](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Ändra JSON-nyttolasten efter behov.
1. Välj **Done** och **Stäng & Verkställ** i **Power Query redigerings fönstret**.

Du bör se önskade ändringar.  

## <a name="next-steps"></a>Efterföljande moment

* Läs om [Power BI anslutnings begrepp](https://docs.microsoft.com/power-bi/desktop-query-overview) för Azure Time Series Insights.

* Läs mer om [Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Se [Time Series Insights ga Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) och [Time Series Insights Preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
