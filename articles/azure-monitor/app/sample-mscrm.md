---
title: Microsoft Dynamics CRM och Azure Application Insights | Microsoft Docs
description: Hämta telemetri från Microsoft Dynamics CRM Online med Application Insights. Genom gång av installation, hämta data, visualisering och export.
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
ms.openlocfilehash: 470f723782ca29409549e0df8e900edf86cd446e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534289"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Genomgång: Aktivera telemetri för Microsoft Dynamics CRM Online med Application Insights
Den här artikeln visar hur du hämtar telemetridata från [Microsoft Dynamics CRM Online](https://www.dynamics.com/) med hjälp av [Azure Application](https://azure.microsoft.com/services/application-insights/)insikter. Vi går igenom hela processen med att lägga till Application Insights skript i ditt program, fånga data och visualisera data.

> [!NOTE]
> [Bläddra i exempel lösningen](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Lägg till Application Insights i den nya eller befintliga CRM Online-instansen
Du kan övervaka ditt program genom att lägga till ett Application Insights SDK i programmet. SDK skickar telemetri till [Application Insights portalen](https://portal.azure.com), där du kan använda våra kraftfulla analys-och diagnostikverktyg, eller exportera data till lagrings utrymmet.

### <a name="create-an-application-insights-resource-in-azure"></a>Skapa en Application Insights-resurs i Azure
1. Hämta [ett konto i Microsoft Azure](https://azure.com/pricing). 
2. Logga in på [Azure Portal](https://portal.azure.com) och Lägg till en ny Application Insights resurs. Det är här som dina data kommer att bearbetas och visas.

    ![Klicka på +, Developer Services Application Insights.](./media/sample-mscrm/01.png)

    Välj ASP.NET som programtypen.
3. Följ instruktionerna för att [Hämta JavaScript SDK-skriptet för appen](../../azure-monitor/app/javascript.md), kopiera JavaScript-kodfragmentet och se till att ersätta Instrumentation-nyckeln med rätt värde för din Application Insights-resurs.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Skapa en JavaScript-webbresurs i Microsoft Dynamics CRM
1. Öppna din CRM Online-instans och logga in med administratörs behörighet.
2. Öppna Microsoft Dynamics CRM-inställningar, anpassningar, anpassa systemet

    ![Microsoft Dynamics CRM-inställningar](./media/sample-mscrm/00001.png)

    ![Inställningar > anpassningar](./media/sample-mscrm/00002.png)

1. Skapa en JavaScript-resurs.

    ![Dialog rutan Ny webb resurs](./media/sample-mscrm/07.png)

    Ge den ett namn, Välj **skript (JScript)** och öppna text redigeraren.

    ![Öppna text redigeraren](./media/sample-mscrm/00004.png)
2. Kopiera koden från Application Insights JavaScript SDK där du konfigurerade Instrumentation-nyckeln. När du kopierar, se till att ignorera skript taggar. Se skärm bilden nedan:

    ![Ställ in Instrumentation-nyckeln](./media/sample-mscrm/000005.png)

    Koden innehåller Instrumentation-nyckeln som identifierar din Application Insights-resurs.
3. Spara och publicera.

    ![Spara och publicera](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Instrument formulär
1. Öppna formuläret konto i Microsoft CRM Online

    ![Konto formulär](./media/sample-mscrm/00007.png)
2. Öppna formulär egenskaper

    ![Formulär egenskaper](./media/sample-mscrm/00008.png)
3. Lägg till JavaScript-webbresursen som du skapade

    ![Menyn Lägg till](./media/sample-mscrm/13.png)

4. Spara och publicera dina formulär anpassningar.

## <a name="metrics-captured"></a>Insamlade mått
Du har nu ställt in telemetri för formuläret. När den används skickas data till din Application Insights-resurs.

Här följer exempel på de data som visas.

#### <a name="application-health"></a>Program hälsa
![Exempel på sid inläsnings tid](./media/sample-mscrm/15.png)

![Exempel på sid visnings diagram](./media/sample-mscrm/16.png)

Webb läsar undantag:

![Diagram över webb läsar undantag](./media/sample-mscrm/17.png)

Klicka på diagrammet för att få mer information:

![Undantags lista](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Användning
![Användare, sessioner och sid visningar](./media/sample-mscrm/19.png)

![Session diagram](./media/sample-mscrm/20.png)

![Webb läsar versioner](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Webbläsare
![Uppdelning av sid inläsnings tid](./media/sample-mscrm/22.png)

![Antal sessioner efter webb läsar version](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Geoplats
![Antal sessioner per land](./media/sample-mscrm/24.png)

![Sessioner och användare efter land](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Webbdelsbegäran i sid visning
![Sammanfattning av sid visning](./media/sample-mscrm/26.png)

![Sök på sid visnings händelser](./media/sample-mscrm/27.png)

![Liknande sidvisningar](./media/sample-mscrm/28.png)

![Egenskaper för sidvisning](./media/sample-mscrm/29.png)

![Sidor per session](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Exempelkod
[Bläddra i exempel koden](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Du kan göra ännu djupare analys om du [exporterar data till Microsoft Power BI](../../azure-monitor/app/export-power-bi.md ).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Exempel på Microsoft Dynamics CRM-lösning
[Här är exempel lösningen som implementeras i Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Lär dig mer
* [Vad är Application Insights?](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights för webb sidor](../../azure-monitor/app/javascript.md)
* [Fler exempel och genom gångar](../../azure-monitor/app/app-insights-overview.md)
