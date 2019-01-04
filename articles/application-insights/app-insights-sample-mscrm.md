---
title: Microsoft Dynamics CRM och Azure Application Insights | Microsoft Docs
description: Hämta telemetri från Microsoft Dynamics CRM Online med hjälp av Application Insights. Genomgång av installationsprogrammet hämtar data, visualisering och export.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 7f3746fc340b382c58f7a9501cb0828a6b6befe8
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976043"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Steg-för-steg-beskrivning: Aktivera telemetri för Microsoft Dynamics CRM Online med hjälp av Application Insights
Den här artikeln visar hur du hämtar telemetridata från [Microsoft Dynamics CRM Online](https://www.dynamics.com/) med [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Vi går genom processen att lägga till Application Insights-skriptet till ditt program, samla in data och datavisualisering.

> [!NOTE]
> [Bläddra exempellösningen](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Lägg till Application Insights i nya eller befintliga CRM Online-instans
Om du vill övervaka programmet du lägga till en Application Insights SDK till ditt program. SDK: N skickar telemetri till den [Application Insights-portalen](https://portal.azure.com), där du kan använda våra kraftfulla analys- och diagnostikverktyg eller exportera data till lagring.

### <a name="create-an-application-insights-resource-in-azure"></a>Skapa en Application Insights-resurs i Azure
1. Hämta [ett konto i Microsoft Azure](https://azure.com/pricing). 
2. Logga in på den [Azure-portalen](https://portal.azure.com) och Lägg till en ny Application Insights-resurs. Det här är där dina data bearbetas och visas.

    ![Klicka på +, Utvecklartjänster, Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Välj ASP.NET som programtypen.
3. Följ anvisningarna för att [hämta JavaScript SDK-skriptet för din app](../azure-monitor/app/javascript.md#set-up-application-insights-for-your-web-page), kopiera JavaScript-kodavsnitt och Ersätt Instrumenteringsnyckeln med rätt värde för Application Insights-resursen.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Skapa en webbresurs i JavaScript i Microsoft Dynamics CRM
1. Öppna din CRM Online-instans och logga in med administratörsbehörighet.
2. Öppna Microsoft Dynamics CRM-inställningar, anpassningar, anpassa systemet

    ![Microsoft Dynamics CRM-inställningar](./media/app-insights-sample-mscrm/00001.png)

    ![Inställningar > anpassningar](./media/app-insights-sample-mscrm/00002.png)

1. Skapa en JavaScript-resurs.

    ![Dialogrutan Ny webbresursen](./media/app-insights-sample-mscrm/07.png)

    Ge den ett namn, Välj **skript (JScript)** och öppna textredigeraren.

    ![Öppna textredigeraren](./media/app-insights-sample-mscrm/00004.png)
2. Kopiera koden från Application Insights JavaScript SDK som du har konfigurerat din Instrumentationsnyckel innan. När du kopierar, se till att ignorera skript-taggar. Se skärmbilden nedan:

    ![Ange instrumenteringsnyckeln](./media/app-insights-sample-mscrm/000005.png)

    Koden innehåller instrumenteringsnyckeln som identifierar din Application insights-resurs.
3. Spara och publicera.

    ![Spara och publicera](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Instrument formulär
1. Öppna formuläret konto i Microsoft CRM Online

    ![Formuläret](./media/app-insights-sample-mscrm/00007.png)
2. Öppna formuläret egenskaper

    ![Egenskaper för formulär](./media/app-insights-sample-mscrm/00008.png)
3. Lägg till JavaScript-webbresursen som du skapade

    ![Menyn Lägg till](./media/app-insights-sample-mscrm/13.png)

4. Spara och publicera dina formuläranpassningar.

## <a name="metrics-captured"></a>Mått som avbildas
Du har nu konfigurerat telemetriinsamlingen för formuläret. När den används kan data skickas till Application Insights-resursen.

Här följer exempel på de data som visas.

#### <a name="application-health"></a>Programmets hälsotillstånd
![Exempel sidinläsningstiden](./media/app-insights-sample-mscrm/15.png)

![Exempeldiagram sidan vyer](./media/app-insights-sample-mscrm/16.png)

Webbläsarundantag:

![Webbläsaren diagrammet med serverundantag](./media/app-insights-sample-mscrm/17.png)

Klicka på diagrammet för att få mer information:

![Undantagslista](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Användning
![Användare, sessioner och sidvyer](./media/app-insights-sample-mscrm/19.png)

![Sessionen diagram](./media/app-insights-sample-mscrm/20.png)

![Versioner av webbläsaren](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Webbläsare
![Analys av inläsningstid](./media/app-insights-sample-mscrm/22.png)

![Antal sessioner per webbläsarversion](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geoplats
![Sessionsantal per land](./media/app-insights-sample-mscrm/24.png)

![Sessioner och användare per land](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Inuti sidan Visa förfrågan
![Sidan Visa sammanfattning](./media/app-insights-sample-mscrm/26.png)

![Sök på sidvisningshändelser](./media/app-insights-sample-mscrm/27.png)

![Liknande sidvisningar](./media/app-insights-sample-mscrm/28.png)

![Egenskaper för sidvisning](./media/app-insights-sample-mscrm/29.png)

![Sidor per session](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Exempelkod
[Bläddra exempelkoden](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Du kan göra djupare analys om du [exportera data till Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Exempellösningen Microsoft Dynamics CRM
[Här är exempellösningen implementeras i Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Läs mer
* [Vad är Application Insights?](app-insights-overview.md)
* [Application Insights för webbsidor](../azure-monitor/app/javascript.md)
* [Fler exempel och genomgångar](app-insights-overview.md)
