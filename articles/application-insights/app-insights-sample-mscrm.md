---
title: Microsoft Dynamics CRM och Azure Application Insights | Microsoft Docs
description: "Hämta telemetri från Microsoft Dynamics CRM Online med hjälp av Application Insights. Genomgång av installationen, som hämtar data, visualisering och export."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: mbullwin
ms.openlocfilehash: c5a651a24fcf5d1fc64922483045c08321a3b89c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Genomgång: Aktivera telemetri för Microsoft Dynamics CRM Online med hjälp av Application Insights
Den här artikeln visar hur du hämtar telemetridata från [Microsoft Dynamics CRM Online](https://www.dynamics.com/) med [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Vi går genom processen att lägga till Application Insights skript i ditt program, data och datavisualisering.

> [!NOTE]
> [Bläddra i exempellösning](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Lägg till Application Insights i nya eller befintliga CRM Online-instans
Om du vill övervaka programmet, du lägger till en Application Insights SDK ditt program. SDK skickar telemetri för den [Application Insights-portalen](https://portal.azure.com), där du kan använda våra kraftfulla analys och diagnosverktyg eller exportera data till lagring.

### <a name="create-an-application-insights-resource-in-azure"></a>Skapa en Application Insights-resurs i Azure
1. Hämta [ett konto i Microsoft Azure](http://azure.com/pricing). 
2. Logga in på den [Azure-portalen](https://portal.azure.com) och lägga till en ny Application Insights-resurs. Detta är där dina data bearbetas och visas.

    ![Klicka på +, Developer Services Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Välj ASP.NET som programtypen.
3. Öppna sidan komma igång och ”övervaka och diagnostisera på klientsidan”.

    ![Kodstycke för infogning i din webbsida](./media/app-insights-sample-mscrm/03.png)

**Håll teckentabellen öppen** medan du gör nästa steg i ett nytt webbläsarfönster. Du behöver koden snart. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Skapa en webbresurs JavaScript i Microsoft Dynamics CRM
1. Öppna din CRM Online-instans och logga in med administratörsbehörighet.
2. Öppna Microsoft Dynamics CRM-inställningar, anpassningar, anpassa systemet

    ![Microsoft Dynamics CRM-inställningar](./media/app-insights-sample-mscrm/00001.png)

    ![Inställningar > anpassningar](./media/app-insights-sample-mscrm/00002.png)

1. Skapa en JavaScript-resurs.

    ![Dialogrutan Ny webbresurs](./media/app-insights-sample-mscrm/07.png)

    Ge det ett namn, Välj **skript (JScript)** och öppna textredigeraren.

    ![Öppna textredigeraren för](./media/app-insights-sample-mscrm/00004.png)
2. Kopiera koden från Application Insights. När du kopierar, se till att ignorera skripttaggar. Se nedan skärmbild:

    ![Ange din instrumentation nyckel](./media/app-insights-sample-mscrm/00005.png)

    Koden innehåller nyckeln instrumentation som identifierar din Application insights-resurs.
3. Spara och publicera.

    ![Spara och publicera](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Betalningsinstrument formulär
1. Öppna formuläret konto i Microsoft CRM Online

    ![Kontoformuläret](./media/app-insights-sample-mscrm/00007.png)
2. Öppna formuläret egenskaper

    ![Egenskaper för formulär](./media/app-insights-sample-mscrm/00008.png)
3. Lägg till JavaScript-webbresurs som du skapade

    ![Menyn Lägg till](./media/app-insights-sample-mscrm/13.png)

4. Spara och publicera dina formuläranpassningar.

## <a name="metrics-captured"></a>Mått som har hämtats
Du har nu konfigurerat telemetriinsamling för formuläret. När den används kan data skickas till Application Insights-resurs.

Här följer exempel på de data som visas.

#### <a name="application-health"></a>Programmets hälsotillstånd
![Exempel sidinläsningstiden](./media/app-insights-sample-mscrm/15.png)

![Exempeldiagram sidan vyer](./media/app-insights-sample-mscrm/16.png)

Webbläsarundantag:

![Webbläsaren undantag diagram](./media/app-insights-sample-mscrm/17.png)

Klicka på diagrammet om du vill ha mer information:

![Listan över undantag](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Användning
![Användare, sessioner och sidvyer](./media/app-insights-sample-mscrm/19.png)

![Sessionen diagram](./media/app-insights-sample-mscrm/20.png)

![Webbläsarversioner](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Webbläsare
![Uppdelning av sidinläsningstiden](./media/app-insights-sample-mscrm/22.png)

![Antal sessioner efter webbläsarversion](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geoplats
![Sessionsantal efter land](./media/app-insights-sample-mscrm/24.png)

![Sessioner och användare per land](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Inuti vyn begäran
![Sidan Visa sammanfattning](./media/app-insights-sample-mscrm/26.png)

![Söka på sidan Visa händelser](./media/app-insights-sample-mscrm/27.png)

![Liknande sidvisningar](./media/app-insights-sample-mscrm/28.png)

![Egenskaper för sidvisning](./media/app-insights-sample-mscrm/29.png)

![Sidor per session](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Exempelkod
[Bläddra exempelkoden](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Du kan göra djupare analys om du [exportera data till Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Exempel Microsoft Dynamics CRM-lösning
[Här är exempellösningen implementeras i Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Läs mer
* [Vad är Application Insights?](app-insights-overview.md)
* [Application Insights för webbsidor](app-insights-javascript.md)
* [Fler exempel och genomgång](app-insights-code-samples.md)
