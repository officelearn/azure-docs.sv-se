---
title: "Felsöka Cloud Services med Programinsikter | Microsoft Docs"
description: "Lär dig hur du felsöker problem med cloud service med hjälp av Application Insights för bearbetning av data från Azure-diagnostik."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Felsöka molntjänster med hjälp av Application Insights
Med [Azure SDK 2.8](https://azure.microsoft.com/downloads/) och tillägg för Azure-diagnostik 1.5, du kan skicka data i Azure-diagnostik för din molntjänst direkt till Application Insights. Loggarna samlas in av Azure-diagnostik&mdash;inklusive programloggarna, Windows-händelseloggar, ETW-loggar och prestandaräknare&mdash;kan skickas till Application Insights. Sedan kan du visualisera informationen i Användargränssnittet för Application Insights-portalen. Du kan sedan använda Application Insights SDK för att få insikter om mått och loggar som kommer från ditt program samt system och data för infrastruktur-nivå som kommer från Azure-diagnostik.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Konfigurera Azure-diagnostik för att skicka data till Application Insights
Följ dessa steg för att konfigurera ditt molntjänstprojekt Azure Diagnostics data ska skickas till Application Insights.

1. Högerklicka på en roll i Visual Studio Solution Explorer och markera **egenskaper** att öppna designern roll.

    ![Solution Explorer rollegenskaper][1]

2. I den **diagnostik** avsnitt av rollen designer väljer den **skicka diagnostikdata till Application Insights** alternativet.

    ![Rollen designer skicka diagnostikdata till application insights][2]

3. I dialogrutan som öppnas väljer du Application Insights-resursen som du vill skicka data till Azure-diagnostik. Dialogrutan kan du välja en befintlig Application Insights-resurs från prenumerationen eller manuellt ange en instrumentation nyckel för Application Insights-resurs. Mer information om hur du skapar en Application Insights-resursen finns [skapar en ny Application Insights-resurs](../application-insights/app-insights-create-new-resource.md).

    ![Välj application insights-resurs][3]

    När du har lagt till Application Insights-resursen instrumentation nyckeln för den här resursen lagras som en konfigurationsinställning för tjänst med namnet **APPINSIGHTS_INSTRUMENTATIONKEY**. Du kan ändra Konfigurationsinställningen för varje tjänstkonfiguration eller miljö. Om du vill göra det, Välj en annan konfiguration från den **tjänstkonfiguration** listan och ange en ny instrumentation nyckel för denna konfiguration.

    ![Välj tjänstkonfiguration][4]

    Den **APPINSIGHTS_INSTRUMENTATIONKEY** Konfigurationsinställningen används av Visual Studio för att konfigurera diagnostik-tillägget med lämpliga resursinformationen Application Insights vid publicering. Konfigurationsinställningen är ett bekvämt sätt att definiera olika instrumentation nycklar för olika konfigurationer. Visual Studio kommer översätta inställningen och infoga i diagnostik tilläggets offentliga konfiguration under publiceringsprocessen. Paketet utdata från Visual Studio innehåller också offentliga konfiguration XML med lämplig Application Insights instrumentation nyckel för att förenkla konfigureringen av tillägget diagnostik med PowerShell. Offentliga config-filer som skapas i mappen tillägg och följer mönstret *PaaSDiagnostics.&lt; RoleName&gt;. PubConfig.xml*. PowerShell-baserade distributioner kan använda det här mönstret för att mappa varje konfiguration till en roll.

4) För att konfigurera Azure-diagnostik för att skicka alla prestandaräknare och Felnivån loggar som samlas in av Azure-diagnostik agenten till Application Insights, aktivera den **skicka diagnostikdata till Application Insights** alternativet. 

    Om du vill konfigurera ytterligare vilka data skickas till Application Insights, måste du manuellt redigera den *diagnostics.wadcfgx* fil för varje roll. Se [konfigurera Azure-diagnostik att skicka data till Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) att lära dig mer om att manuellt uppdatera konfigurationen.

När Molntjänsten är konfigurerad för att skicka Azure-diagnostikdata till application insights, kan du distribuera den till Azure normalt, kontrollera att tillägget för Azure-diagnostik är aktiverat. Mer information finns i [publicering av en tjänst i molnet med hjälp av Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Visa data i Azure-diagnostik i Application Insights
Azure diagnostisk telemetri visas i Application Insights-resurs som konfigurerats för Molntjänsten.

Azure diagnostics logga typer karta till Application Insights begrepp på följande sätt:

* Prestandaräknare visas som anpassade mått i Application Insights.
* Windows-händelseloggar visas som spårningar och anpassade händelser i Application Insights.
* Program loggar, ETW-loggar och diagnostik infrastruktur loggar visas som spåren i Application Insights.

Om du vill visa data i Azure-diagnostik i Application Insights gör du något av följande:

* Använd [Metrics explorer](../application-insights/app-insights-metrics-explorer.md) visualisera alla anpassade prestandaräknare eller ett antal olika typer av händelser i händelseloggen i Windows.

    ![Anpassade mått i Metrics Explorer][5]

* Använd [Sök](../application-insights/app-insights-diagnostic-search.md) att söka i spårningsloggar som skickas av Azure-diagnostik. Till exempel om ett ohanterat undantag har orsakat rollen till krascher och återanvänd, information om undantaget visas i den *programmet* kanal för *Windows-händelseloggen*. Du kan använda Sök för att titta i händelseloggen i Windows-fel och få fullständig stackspårningen för undantaget för att hitta orsaken till problemet.

    ![Sök spårningar][6]

## <a name="next-steps"></a>Nästa steg
* [Lägg till Application Insights SDK i din molntjänst](../application-insights/app-insights-cloudservices.md) att skicka information om begäranden, undantag, beroenden och eventuella anpassade telemetri från ditt program. När den kombineras med Azure-diagnostik data den här informationen kan du få en fullständig överblick över dina program och system i samma Application Insights-resurs.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
