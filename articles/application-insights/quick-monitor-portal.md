---
title: Övervaka din ASP.NET-livewebbapp med Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att snabbt konfigurera en ASP.NET-webbapp för övervakning med Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/13/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: db8aa2d1bb5d79b5d2c9b04789b4ac18fbec5897
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664598"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Börja övervaka din ASP.NET-webbapp

Med Azure Application Insights kan du enkelt övervaka en webbapp med avseende på tillgänglighet, prestanda och användning.  Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem.  Med hjälp av informationen du samlar in från Application Insights om appens prestanda och effektivitet kan du göra välgrundade val för att underhålla och förbättra ditt program.

Den här snabbstarten visar hur du lägger till Application Insights till en befintlig ASP.NET-webbapp och börjar analysera livestatistik. Det är bara en av de olika metoderna du kan använda för att analysera din app. Om du inte har en ASP.NET-webbapp kan du skapa en genom att följa [snabbstarten för att skapa en ASP.NET-webbapp](../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här snabbstarten behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="enable-application-insights"></a>Aktivera Application Insights

1. Öppna projektet i Visual Studio 2017.
2. Välj **Configure Application Insights** (Konfigurera Application Insights) på projektmenyn. Visual Studio lägger till Application Insights SDK för programmet.

    > [!IMPORTANT]
    > Processen för att lägga till Application Insights varierar beroende på malltypen i ASP.NET. Om du använder mallen **Tom** eller **Azure-mobilapp** väljer du **Projekt** > **Lägg till Application Insights Telemetry**. För alla andra ASP.NET-mallar läser du anvisningarna i steget ovan. 

3. Klicka på **Get Started** (Kom igång) (tidigare versioner av Visual Studio har knappen **Start Free** (Börja kostnadsfritt) i stället).

    ![Lägga till Application Insights i Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Välj din prenumeration och klicka på **Registrera**.

5. Kör ditt program genom att antingen välja alternativet för att **starta felsökning** på menyn **Felsök** eller genom att trycka på tangenten F5.

## <a name="confirm-app-configuration"></a>Bekräfta appkonfiguration

Application Insights samlar in telemetridata för tillämpningsprogrammet oavsett var det körs. Gör så här om du vill börja granska dessa data:

1. Öppna Application Insights genom att klicka på **Visa** -> **Andra fönster** -> **Application Insights Search**.  Du kan se telemetrin från den aktuella sessionen.<BR><br>![Telemetri i Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Klicka på den första begäran i listan (GET Home/Index i det här exemplet) för att se mer information. Observera att både statuskoden och svarstiden ingår tillsammans med annan värdefull information om begäran.<br><br>![Svarsinformation i Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

Nu kan du öppna Application Insights i Azure-portalen för att visa olika detaljer om programmet som körs.

1. Högerklicka på mappen **Connected Services Application Insights** i Solution Explorer och klicka på alternativet för att **öppna Application Insights-portalen**.  Du ser viss information om ditt program och många andra alternativ.

    ![Programkarta](media/quick-monitor-portal/overview-001.png)

2. Klicka på **Programkarta** för en visuell layout av beroenden mellan programkomponenterna.  För varje komponent visas KPI:er som belastning, prestanda, fel och varningar.

    ![Programkarta](media/quick-monitor-portal/application-map-001.png)

3. Klicka på **App Analytics**-ikonen ![Programavbildning](media/quick-monitor-portal/app-analytics-icon.png) på någon av programkomponenterna.  Då öppnas **Application Insights Analytics**, med ett funktionsrikt frågespråk för att analysera alla data som samlas in av Application Insights.  I det här fallet skapas en fråga som återger antalet begäranden som ett diagram.  Du kan skriva egna frågor för att analysera andra data.

    ![Analytics](media/quick-monitor-portal/analytics.png)

4. Återgå till **översiktssidan** och klicka på **Live Stream**.  Här ser du livestatistik om programmet när medan det körs.  Det innehåller information som antalet inkommande begäranden, varaktigheten för dessa begäranden och fel som inträffar.  Du kan även granska kritiska prestandavärden, till exempel processor och minne.

    ![Direktsänd ström](media/quick-monitor-portal/live-stream.png)

Om du är redo att vara värd för programmet i Azure kan publicera den nu. Följ stegen i informationen om att [skapa en snabbstart för ASP.NET-webbappen](../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

## <a name="next-steps"></a>Nästa steg
Du har aktiverat ditt program för övervakning av Azure Application Insights i den här snabbstartsguiden.  Fortsätt till självstudierna för att lära dig att använda övervakningsstatistik och identifiera problem i programmet.

> [!div class="nextstepaction"]
> [Azure Application Insights-självstudier](app-insights-tutorial-runtime-exceptions.md)
