---
title: "Åtgärda 502 Felaktig gateway, 503-tjänsten inte är tillgänglig | Microsoft Docs"
description: "Felsöka 502 Felaktig gateway och 503 tjänsten tillgänglig fel i webbappen i Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "502 Felaktig gateway 503-tjänsten tillgänglig, fel 503, fel 502"
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Felsök HTTP-fel ”502 Felaktig gateway” och ”503 inte tillgänglig” i Azure-webbappar
”502 Felaktig gateway” och ”503 inte tillgänglig” är vanliga fel i ditt webbprogram som finns i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Den här artikeln hjälper dig att felsöka felen.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du även filen en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och klicka på **Get Support**.

## <a name="symptom"></a>Symtom
När du bläddrar till webbappen returnerar ett HTTP ”502 felaktig Gateway” fel eller en HTTP-fel ”503 inte tillgänglig”.

## <a name="cause"></a>Orsak
Det här problemet beror ofta på nivån programproblem som:

* begäranden som tar lång tid
* program med hög minne/processor
* programmet kraschar på grund av ett undantag.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Felsökningssteg för att lösa ”502 Felaktig gateway” och ”503 inte tillgänglig” fel
Felsökning kan delas in i tre olika uppgifter i sekventiell ordning:

1. [Observera och övervaka programmets beteende](#observe)
2. [Samla in data](#collect)
3. [Åtgärda problemet](#mitigate)

[App Service Web Apps](/services/app-service/web/) ger olika alternativ i varje steg.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observera och övervaka programmets beteende
#### <a name="track-service-health"></a>Spåra tjänstens hälsa
Microsoft Azure publicizes varje gång en tjänst avbrott eller prestanda försämras. Du kan spåra hälsotillståndet för tjänsten på den [Azure Portal](https://portal.azure.com/). Mer information finns i [spåra tjänstens hälsa](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Övervaka ditt webbprogram
Det här alternativet kan du ta reda på om ditt program är har problem. I din webbapps blad klickar du på den **begäranden och fel** panelen. Den **mått** bladet visar alla mått som du kan lägga till.

Några av de mått som du kanske vill övervaka för webbappen

* Genomsnittlig minne arbetsminne
* Genomsnittlig svarstid
* CPU-tid
* Arbetsminnet för minne
* Begäranden

![övervaka webbprogram ska lösa HTTP-fel 502 Felaktig gateway och 503 inte tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Mer information finns i:

* [Övervakaren Web Apps i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Samla in data
#### <a name="use-the-azure-app-service-support-portal"></a>Använda portalen för Azure App Service-stöd
Web Apps ger dig möjlighet att felsöka problem med ditt webbprogram genom att titta på http-loggar, händelseloggar, process Dumpar och mycket mer. Du kan komma åt den här informationen med hjälp av vår portal Support vid **http://&lt;appens namn >.scm.azurewebsites.net/Support**

Stöd för Azure App Service-portalen innehåller tre separata flikar som stöd för ett vanligt scenario för felsökning tre steg:

1. Se aktuella beteende
2. Analysera genom att samla in diagnostikinformation och köra de inbyggda analyzers
3. Minimera

Om problemet sker just nu, klickar du på **analysera** > **diagnostik** > **diagnostisera nu** att skapa en diagnostisk session, vilket samlar in http-loggar, loggar i Loggboken, minne Dumpar, PHP felloggarna och PHP bearbeta rapporten.

När data samlas in, kommer den även kör en analys på data och ger dig en HTML-rapport.

Om du vill hämta data, som standard lagras den i mappen D:\home\data\DaaS.

Mer information om stöd för Azure App Service-portalen finns [nya uppdateringar till stöd för plats-tillägg för Azure Websites](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Använd Kudu-Felsökningskonsolen
Web Apps levereras med en Felsökningskonsolen som du kan använda för felsökning, utforska, ladda upp filer, samt JSON-slutpunkter för att hämta information om din miljö. Detta kallas den *Kudu konsolen* eller *SCM instrumentpanelen* för ditt webbprogram.

Du kan komma åt den här instrumentpanelen genom att gå till länken **https://&lt;appens namn >.scm.azurewebsites.net/**.

Några av de saker som Kudu tillhandahåller är:

* inställningar för ditt program
* loggström
* diagnostiska dump
* Felsöka konsolen där du kan köra Powershell-cmdlets och grundläggande DOS-kommandon.

En annan användbar funktion i Kudu är att, om ditt program är att första chans undantag, kan du använda Kudu och verktyget SysInternals Procdump att skapa minne Dumpar. Dessa minnesdumpar är ögonblicksbilder av processen och ofta kan hjälpa dig att felsöka mer komplicerade problem med ditt webbprogram.

Mer information om funktionerna i Kudu finns [Azure Websites online-verktyg som du bör känna till om](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Åtgärda problemet
#### <a name="scale-the-web-app"></a>Skala webbprogrammet
I Azure App Service kan för bättre prestanda och genomflöde, du justera skalan där du kör programmet. Skala upp ett webbprogram omfattar två relaterade åtgärder: ändra din programtjänstplan till en högre prisnivå och konfigurera vissa inställningar när du har växlat till högre prisnivå.

Läs mer om att skala [skala en webbapp i Azure App Service](web-sites-scale.md).

Dessutom kan du välja att köra programmet på mer än en instans. Detta inte bara ger mer bearbetningskapacitet, men ger dig även vissa delar av feltolerans. Om processen stängs av på en instans, kommer den andra instansen fortfarande begäranden.

Du kan ange skalning till manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd säkert att AutoHeal
Säkert att AutoHeal återanvänder arbetsprocessen för din app baserat på dina inställningar (till exempel konfigurationsändringar begäranden, minnesbaserade gränser eller den tid som krävs för att utföra en begäran). I de flesta fall, är omarbetning av processen det snabbaste sättet att återställa från ett problem. Även om du kan alltid starta om webbprogrammet från direkt i Azure Portal, gör säkert att AutoHeal det automatiskt åt dig. Allt du behöver göra är att lägga till vissa utlösare i rot-web.config för ditt webbprogram. Observera att de här inställningarna fungerar på samma sätt även om programmet inte är en .net en.

Mer information finns i [automatisk återställning Azure webbplatser](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Starta om webbprogrammet
Det här är ofta det enklaste sättet att återställa engångsproblem. På den [Azure Portal](https://portal.azure.com/), på bladet för ditt webbprogram har du alternativ för att stoppa eller starta om din app.

 ![Starta om appen för att lösa HTTP-fel 502 Felaktig gateway och 503 inte tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Du kan också hantera ditt webbprogram med hjälp av Azure Powershell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

