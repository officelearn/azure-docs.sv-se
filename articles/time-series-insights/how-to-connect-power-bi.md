---
title: Anslut din miljö till Power BI-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du ansluter Azure Time Series Insights till Power BI för att dela, rita och visa data i hela organisationen.
author: deepakpalled
ms.author: dpalled
manager: diviso
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 680b3c5a9548fa06d0139bd441b5583c27427a77
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020784"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Visualisera data från Azure Time Series Insights i Power BI

Azure Time Series Insights är en plattform för att lagra, hantera, fråga och visualisera Time Series-data i molnet. [Power BI](https://powerbi.microsoft.com) är ett företags analys verktyg med omfattande visualiserings funktioner som gör att du kan dela insikter och resultat i hela organisationen. Båda tjänsterna kan nu integreras för att få det bästa av både Azure Time Series Insights "inbyggda visualiserings funktioner" och Power BI.

Du lär dig följande:

* Ansluta Azure Time Series Insights till Power BI med hjälp av Cloud Connector
* Skapa visuella objekt med dina data i Power BI
* Publicera rapporten för att Power BI och dela den med resten av organisationen

I slutet får du lära dig hur du visualiserar Time Series-data via Azure Time Series Insights och förbättrar den med data visualiseringen stark och enkel delning av Power BI.

Se till att du registrerar dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Förutsättningar

* Hämta och installera den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Ha eller skapa en [Azure Time Series Insights Gen2-miljö](./how-to-provision-manage.md)

> [!IMPORTANT]
>
> * Anslutningen stöds för närvarande i Azure Time Series Insights Gen2-miljöer som kon figurer ATS med **endast varm lagring**.
> * Om du har gäst åtkomst till Azure Time Series Insights Gen2-miljön från en annan Azure AD-klient kommer du inte att kunna komma åt anslutningen. Läs om [miljö åtkomst principer](./concepts-access-policies.md).

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Anslut data från Azure Time Series Insights till Power BI

Följ dessa steg om du vill ansluta din Azure Time Series Insights-miljö till Power BI:

1. Öppna Azure Time Series Insights Explorer
1. Exportera data som en fråga eller som rå data
1. Öppna Power BI Desktop
1. Läs in från anpassad fråga

### <a name="export-data-into-power-bi-desktop"></a>Exportera data till Power BI Desktop

Så här kommer du igång:

1. Öppna Azure Time Series Insights Explorer och granska dina data.
1. När du har skapat en vy som du är nöjd med, navigerar du till List menyn **fler åtgärder** och väljer **Anslut till Power BI**.

    [![Azure Time Series Insights Explorer-export](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Ange parametrarna i den här fliken:

   1. Ange en relativ tidsram att visa. Om du är nöjd med din befintliga vy lämnar du den som en **befintlig tidsram**.

   1. Välj mellan **aggregerade** och **råa händelser**.

       > [!NOTE]
       > Du kan alltid aggregera dina data senare i Power BI, men du kan inte återgå till rå data efter AGG regering.

       > [!NOTE]
       > Det finns en gräns för antal 250 000-händelser för rå data på händelse nivå.

       [![Anslut](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Om du inte har konfigurerat din Azure Time Series Insightss miljö med ett **varmt Arkiv** får du en varning.

       [![Varning om varma lager](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Du kan konfigurera din befintliga instans för **varm lagring** i Azure Portal.

1. Välj **Kopiera fråga till Urklipp**.
1. Starta Power BI Desktop nu.
1. I Power BI Desktop på fliken **Start** väljer du **Hämta data** i det övre vänstra hörnet och sedan **mer**.

    [![List rutan hem](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Sök efter **Azure Time Series Insights**, Välj **Azure Time Series Insights (beta)** och **Anslut**.

    [![Anslut Power BI till Azure Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Du kan också navigera till fliken **Azure** , välja **Azure Time Series Insights (beta)** och sedan **ansluta**.

1. Dialog rutan meddelande visas med frågan om behörighet att ansluta till resurser från tredje part. Välj **Fortsätt**.

    [![Välj Skapa anpassad fråga](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. I list menyn under **data källa** väljer du **Skapa anpassad fråga**. Klistra in från Urklipp i det valfria fältet **anpassad fråga (valfritt)** nedan och tryck sedan på **OK**.

    [![Skicka i den anpassade frågan och välj OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Data tabellen kommer nu att läsas in. Tryck på **load** för att läsa in i Power BI.

    [![Granska inlästa data i tabellen och välj load](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Om du har slutfört de här stegen kan du gå vidare till nästa avsnitt.

## <a name="create-a-report-with-visuals"></a>Skapa en rapport med visuella objekt

Nu när du har importerat data till Power BI är det dags att bygga en rapport med visuella objekt.

1. Kontrol lera att du har valt **rapportvyn** på vänster sida av fönstret.

    [![Skärm bild som visar ikonen rapportvy.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Välj önskat visualiserings alternativ i kolumnen **visualiseringar** . Välj till exempel **linje diagram**. Ett tomt linje diagram läggs till på din arbets yta.

1. I listan **fält** väljer du **_Timestamp** och drar den till fältet **axel** för att visa objekt längs X-axeln. Se till att växla till **_Timestamp** till som värde för **axeln** (Standardvärdet är **date-hierarkin**).

    [![Skärm bild som visar timestamp-menyn med _Timestamp valt.](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1. Återigen, i listan **fält** , väljer du **TimeSeriesId** och drar den till fältet **värden** för att visa objekt utmed Y-axeln.

    [![Skapa ett linjediagram](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Om du vill lägga till ett annat diagram på arbets ytan väljer du var som helst på arbets ytan utanför linje diagrammet och upprepar den här processen.

    [![Skapa ytterligare diagram som ska delas](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

När du har skapat rapporten kan du publicera den till Power BI repor ting Services.

## <a name="advanced-editing"></a>Avancerad redigering

Om du redan har läst in en data uppsättning i Power BI men vill ändra frågan (till exempel parametrarna för datum/tid eller miljö-ID), kan du göra detta via Avancerad redigerare-funktionerna i Power BI. Läs mer i [Power BI-dokumentationen](/power-bi/desktop-query-overview) .

Som en översikt:

1. I Power BI Desktop väljer du **Redigera frågor**.
1. Tryck på **avancerad redigerare**.

    [![Redigera frågor i Avancerad redigerare](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Ändra JSON-nyttolasten efter behov.
1. Välj **Done** och **Stäng & Verkställ** i **Power Query redigerings fönstret**.

Gränssnittet visar nu de önskade ändringarna som du har använt.  

## <a name="next-steps"></a>Nästa steg

* Läs om [Power BI anslutnings begrepp](/power-bi/desktop-query-overview) för Azure Time Series Insights.

* Läs mer om [Power BI Desktop](/power-bi/desktop-query-overview).