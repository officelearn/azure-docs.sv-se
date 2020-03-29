---
title: Ansluta din miljö till Power BI – Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du ansluter Azure Time Series Insights till Power BI för att dela, kartlägga och visa data i hela organisationen.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863850"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualisera data från Time Series Insights i Power BI

Azure Time Series Insights är en plattform för lagring, hantering, frågor och visualisering av tidsseriedata i molnet. [Power BI](https://powerbi.microsoft.com) är ett affärsanalysverktyg med omfattande visualiseringsfunktioner som gör att du kan dela insikter och resultat i hela organisationen. Båda tjänsterna kan nu integreras för att få ut det bästa av både Time Series Insights inneboende visualiseringsfunktioner och Power BI:s.

Du lär dig följande:

* Anslut Time Series Insights till Power BI med molnkontakten
* Skapa visuella objekt med dina data i Power BI
* Publicera rapporten i Power BI och dela med resten av organisationen

I slutet får du lära dig att visualisera tidsseriedata via Azure Time Series Insights och förbättra dem med de starka datavisualiseringsfunktionerna och de enkla delningsfunktionerna i Power BI.

Se till att registrera dig för en [kostnadsfri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Krav

* Ladda ned och installera den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Har eller skapar en [förhandsversion av Azure Time Series Insights](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Power BI-kontakten stöds för närvarande i Time Series Insights Preview *pay-as-you-go-miljöer som* konfigurerats för **Warm Store**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Ansluta data från Time Series Insights till Power BI

Så här ansluter du time series insights-miljön till Power BI:

1. Utforskaren För öppna tidsserieinsikter
1. Exportera data som en fråga eller som rådata
1. Öppna Power BI-skrivbordet
1. Läsa in från anpassad fråga

### <a name="export-data-into-power-bi-desktop"></a>Exportera data till Power BI-skrivbordet

Så här kommer du igång:

1. Öppna Förhandsgranskningsutforskaren för Time Series Insights och sortera dina data.
1. När du har skapat en vy som du är nöjd med navigerar du till den **nedrullningsbara** menyn Fler åtgärder och väljer **Anslut till Power BI**.

    [![Förhandsversion av Utforskaren för Tidsseriestatistik](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Ställ in parametrarna på den här fliken:

   1. Ange en relativ tidsram som ska visas. Om du är nöjd med din befintliga vy lämnar du den här som **befintlig tidsram**.
   
   1. Välj mellan **aggregerade** och **råa händelser**. 
   
       > [!NOTE]
       > Du kan alltid aggregera dina data senare i Power BI, men du kan inte återgå till rådata efter aggregering. 
       
       > [!NOTE]
       > Det finns en 100 K-händelseräkningsgräns för raw event-nivådata.

       [![Ansluta](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Om du inte har konfigurerat time series insights-instansen för **Warm Store**får du en varning.

       [![Varning för varm butik](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Du kan konfigurera din befintliga instans för **Warm Store** i Azure-portalen.

1. Välj **Kopiera fråga till Urklipp**.
1. Starta nu Power BI Desktop.
1. I Power BI Desktop på fliken **Start** väljer du **Hämta data** i det övre vänstra hörnet och sedan **Mer**.

    [![Rullgardinsmenyn för hemmet](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Sök efter **Time Series Insights**, välj **Azure Time Series Insights (Beta)** och **anslut**sedan .

    [![Anslut Insikter i Power BI till Tidsserie](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Du kan också navigera till fliken **Azure,** välja **Azure Time Series Insights (Beta)** och sedan **anslut**.
    
1. En meddelandedialogruta visar att du ber om tillstånd att ansluta till resurser från tredje part. Välj **fortsätt**.

    [![Välj Skapa anpassad fråga](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. I den nedrullningsliga menyn under **Datakälla**väljer du **Skapa anpassad fråga**. Klistra in från Urklipp i det **valfria anpassade frågefältet (valfritt)** nedan och tryck sedan på **OK**.

    [![Skicka den anpassade frågan och välj OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Datatabellen läses nu in. Tryck **på Ladda** in i Power BI.

    [![Granska inlästa data i tabellen och välj Läs in](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Om du har slutfört de här stegen går du vidare till nästa avsnitt.

## <a name="create-a-report-with-visuals"></a>Skapa en rapport med visuella objekt

Nu när du har importerat data till Power BI är det dags att skapa en rapport med visuella objekt.

1. Kontrollera att du har markerat **rapportvyn** till vänster i fönstret.

    [![Markera rapportvyn](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Välj ditt visuella objekt i kolumnen **Visualiseringar.** Välj till exempel **Linjediagram**. Detta kommer att lägga till ett tomt linjediagram på arbetsytan.
 
1.  I listan **Fält** väljer du **Tidsstämpel** och drar den till fältet **Axel** för att visa objekt längs X-axeln.

1.  I listan **Fält** väljer du återigen **TimeSeriesId** och drar det till fältet **Värden** för att visa objekt längs Y-axeln.

    [![Skapa ett linjediagram](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Om du vill lägga till ett annat diagram på arbetsytan markerar du var som helst på arbetsytan utanför linjediagrammet och upprepar den här processen.

    [![Skapa ytterligare diagram att dela](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

När du har skapat rapporten kan du publicera den i Power BI Reporting Services.

## <a name="advanced-editing"></a>Avancerad redigering

Om du redan har läst in en datauppsättning i Power BI men vill ändra frågan (till exempel parametrar för datum/tid eller miljö-ID) kan du göra detta via Power BI:s avancerade redigerarfunktioner. Mer information finns i [Power BI-dokumentationen.](https://docs.microsoft.com/power-bi/desktop-query-overview)

Som en översikt:

1. Välj Redigera frågor **i**Power BI Desktop .
1. Tryck **på Avancerad redigerare**.

    [![Redigera frågor i den avancerade redigeraren](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Ändra JSON-nyttolasten efter behov.
1. Välj **Klar** och **stäng sedan & tillämpas** i Power Query **Editor-fönstret**.

Gränssnittet återspeglar nu de önskade ändringar du har tillämpat.  

## <a name="next-steps"></a>Efterföljande moment

* Läs om [Power BI-anslutningskoncept](https://docs.microsoft.com/power-bi/desktop-query-overview) för Azure Time Series Insights.

* Läs mer om [Power BI-skrivbordet](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Läs [Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) och Time Series [Insights Preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
