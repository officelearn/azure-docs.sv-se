---
title: Azure Application Insights för ASP.NET Core | Microsoft Docs
description: Övervaka webbprogram för tillgänglighet, prestanda och användning.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 2245fcdaa8b7e85ea37e9af9c939cd188c4d7ed9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights för ASP.NET Core

Azure Application Insights ger detaljerad övervakning av webbprogrammet nivån i koden. Du kan enkelt kan övervaka ditt webbprogram för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem.

Den här artikeln guidar dig igenom hur du skapar en ASP.NET Core [Razor sidor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) program i Visual Studio och hur du börja övervaka med Azure Application Insights.

## <a name="prerequisites"></a>Förutsättningar

- NET Core 2.0.0 SDK eller senare.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.3 eller senare med ASP.NET och web development arbetsbelastningen.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Skapa ett ASP.NET Core-projekt i Visual Studio

1. Högerklicka på och starta **Visual Studio 2017** som administratör.
2. Välj **filen** > **nya** > **projekt** (Ctrl-Skift-N).

   ![Skärmbild av nya projekt-menyn i Visual Studio-fil](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Expandera **Visual C#** > Välj **.NET Core** > **webbprogram för ASP.NET Core**. Ange en **namn** > **lösningsnamn** > Kontrollera **Skapa ny Git-lagringsplats**.

   ![Skärmbild av Visual Studio filen guiden](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Välj **.Net Core** > **ASP.NET Core 2.0** **webbprogrammet** > **OK**.

    ![Skärmbild av Visual Studio nya projekt markeringen Arkivmenyn](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>lägg till Application Insights Telemetry

1. Välj **projekt** > **Lägg till Application Insights Telemetry...** (Du kan också högerklicka på **anslutna tjänster** > Lägg till ansluten tjänst.)

    ![Skärmbild av Visual Studio nya projekt markeringen Arkivmenyn](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Välj **Start Free** (Starta kostnadsfritt).

    ![Skärmbild av Visual Studio nya projekt markeringen Arkivmenyn](./media/app-insights-asp-net-core/0005-start-free.png)

3. Välj en lämplig **prenumeration** > **resurs** > och huruvida du vill tillåta samlingen över 1 GB per månad > **registrera**.

    ![Skärmbild av Visual Studio nya projekt markeringen Arkivmenyn](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Ändrar Made till ditt projekt

Application Insights är mycket låg belastning. Granska de ändringar som gjorts i projektet genom att lägga till Application Insights telemetri:

Välj **visa** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projekt** > **ändringar**

- Totalt antal fyra ändringar:

  ![Skärmbild av filer som ändrats genom att lägga till Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- En ny fil skapas:

   **ConnectedService.json**

  ![Skärmbild av filer som ändrats genom att lägga till Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Tre filer ändras:

  **appsettings.json**

   ![Skärmbild av filer som ändrats genom att lägga till Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Skärmbild av filer som ändrats genom att lägga till Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.CS**

   ![Skärmbild av filer som ändrats genom att lägga till Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Syntetiska transaktioner med PowerShell

Starta din app och klicka på runt på länkar manuellt kan användas för att skapa test-trafik. Dock är det ofta vara praktiskt att skapa en enkel syntetisk transaktion i PowerShell.

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio Express i IIS-ikon](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Kopiera URL-adressen från adressfältet i webbläsaren. Det är i formatet http://localhost:{random portnummer}

   ![Skärmbild av adressfältet i webbläsaren url](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Kör följande PowerShell-slinga för att skapa 100 syntetiska transaktioner mot testa appen. Ändra portnumret efter **localhost:** så att den matchar den url som du kopierade i föregående steg.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Öppna Application Insights-portalen

Starta Application Insights för att visa transaktioner och bekräfta att data som samlas in när du har kört PowerShell från föregående avsnitt. 

Visual Studio-menyn väljer du **projekt** > **Programinsikter** > **öppna Application Insights-portalen**

   ![Skärmbild av Application Insights-översikt](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> I skärmbilden ovan **Liveströmning**, **inläsningstid för sidvisning**, och **, misslyckade begäranden** har för närvarande inte samlats in. I nästa avsnitt vägleder genom att lägga till varje. Om du redan samlar **Liveströmning**, och **inläsningstid för sidvisning**, Följ stegen för att endast **, misslyckade begäranden**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Samla in misslyckade begäranden, direktsänd dataström och vyn sida inläsningstid

### <a name="failed-requests"></a>Misslyckade förfrågningar

Tekniskt **, misslyckade begäranden** samlas in, men inget har genomförts ännu. För att snabbare längs anpassade undantag kan läggas till befintliga projektet för att simulera en verkliga undantag. Om din app fortfarande körs i Visual Studio innan du fortsätter **stoppa felsökning** (SKIFT + F5)

1. I **Solution Explorer** > Expandera **sidor** > **About.cshtml** > Öppna **About.cshtml.cs**.

   ![Skärmbild av Visual Studio Solution Explorer](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Lägga till ett undantag under ``Message=`` > Spara ändringarna i filen.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Skärmbild av undantag](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Direktsänd ström

Åtkomst till direktsänd dataström med funktionerna i Application Insights med ASP.NET Core update så att den **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet-paket.

I Visual Studio väljer **projekt** > **hantera NuGet-paket** > **Microsoft.ApplicationInsights.AspNetCore** > Version **2.2.0** > **uppdatering**.

  ![Skärmbild av NuGet Package Manager](./media/app-insights-asp-net-core/0017-update-nuget.png)

Konfigurera flera meddelanden ska ske, Läs och Godkänn om du samtycker till ändringarna.

### <a name="page-view-load-time"></a>Vyn sida inläsningstid

1. I Visual Studio navigerar du till **Solution Explorer** > **sidor** > två filer kommer att behöva ändras: **_Layout.cshtml**, och **_ ViewImports.cshtml**

2. I **_ViewImports.cshtml**, lägga till:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Skärmbild av koden ändras till _ViewImports.cshtml](./media/app-insights-asp-net-core/00018-view-imports.png)

3. I **_Layout.cshtml** Lägg till rad under innan den ``</head>`` tagg, men före eventuella andra skript.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Skärmbild av koden ändras till layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Det gick inte att testa begäranden, vyn sida inläsningstid, direktsänd dataström

Nu när du har slutfört föregående steg kan du testa och bekräfta att allt fungerar.

1. Kör appen genom att klicka på IIS Express ![Skärmbild av Visual Studio Express i IIS-ikon](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Navigera till den **om** att utlösa test-undantag. (Om du kör i felsökningsläge, du måste klicka på **Fortsätt** i Visual Studio innan undantaget hanteras av Application Insights.)

3. Kör skriptet simulerade PowerShell transaktion från tidigare (du kan behöva justera portnumret i skriptet.)

4. Om program insikter översikt inte är fortfarande öppen Visual Studio-menyn väljer **projekt** > **Programinsikter** > **öppna program Insights-portalen**. 

   > [!TIP]
   > Om du inte ser den nya trafiken ännu, kontrollera den **tidsintervallet** och på **uppdatera**.

   ![Skärmbild av översikt](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Välj en direktsänd dataström

   ![Skärmbild av Live mått dataström](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Om din PowerShell skriptet körs fortfarande du bör se live statistik, och om den har stoppats kör skriptet igen med direktsänd dataström öppna.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK jämförelse

Application Insights-produktgruppen har arbetat hårt för att få så nära funktionsparitet som möjligt mellan den [fullständig .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) och .net Core SDK. 2.2.0 versionen av den [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) för Application Insights i stort sett har stängts funktionen mellanrum.

Information om skillnaderna och kompromisser mellan [.NET och .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Jämförelse av SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live mått**      | **+** |**-** | **+** |
   | **Servern telemetri kanal** | **+** |**-** | **+**|
   |**Anpassningsbar provtagning**| **+** | **-** | **+**|
   | **SQL-Beroendeanrop**     | **+** |**-** | **+**|
   | **Prestandaräknare*** | **+** | **-**| **-**|

_Prestandaräknare_ refererar till i den här kontexten [serversidan prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) som processor, minne och diskanvändning.

## <a name="open-source-sdk"></a>Öppen källkod SDK
[Läsa och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Nästa steg
* [Utforska användare flödar](app-insights-usage-flows.md) att förstå hur användarna navigera i din app.
* [Konfigurera ögonblicksbild samling](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) att se status för källkoden och variabler för tillfället ett undantag.
* [Använd API](app-insights-api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md) Kontrollera din app hela tiden från hela världen.
