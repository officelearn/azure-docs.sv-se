---
title: Åtgärda 502 Felaktig gateway, 503 tjänsten otillgänglig fel – Azure App Service | Microsoft Docs
description: Felsöka 502 Felaktig gateway- och 503-tjänsten inte tillgänglig fel i din app i Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 Felaktig gateway, 503 tjänsten otillgänglig, fel 503, fel 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5edd3e51e83b5ab324d1e110a1882b20d935a9b5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899136"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Felsöka HTTP-fel ”502 Felaktig gateway” och ”503 tjänsten ej tillgänglig” i Azure App Service
”502 Felaktig gateway” och ”503 tjänsten ej tillgänglig” är vanliga fel i din app som finns på [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Den här artikeln hjälper dig att felsöka de här felen.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan alternativt kan du även skicka en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och klicka på **få Support**.

## <a name="symptom"></a>Symtom
När du bläddrar till appen, returneras ett HTTP ”502 felaktig Gateway” fel eller en HTTP-fel ”503 tjänsten är inte tillgänglig”.

## <a name="cause"></a>Orsak
Det här problemet beror ofta på nivån programfel, till exempel:

* begäranden som tar lång tid
* program med hög minne/CPU
* program som kraschar på grund av ett undantag.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Felsökningssteg för att lösa ”502 Felaktig gateway” och ”503 tjänsten ej tillgänglig”-fel
Felsökning kan delas in i tre olika uppgifter i sekventiell ordning:

1. [Se och övervaka programmets beteende](#observe)
2. [Samla in data](#collect)
3. [Åtgärda problemet](#mitigate)

[App Service](overview.md) ger dig olika alternativ i varje steg.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Se och övervaka programmets beteende
#### <a name="track-service-health"></a>Spåra tjänstehälsa
Microsoft Azure publicizes varje gång som en försämring av tjänsten avbrott eller prestanda. Du kan spåra hälsotillståndet för tjänsten på den [Azure-portalen](https://portal.azure.com/). Mer information finns i [spåra tjänstehälsa](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Övervaka din app
Det här alternativet kan du ta reda på om ditt program är har problem. Appens bladet klickar du på den **förfrågningar och fel** panelen. Den **mått** bladet visar alla mått som du kan lägga till.

Några av de mått som du kan övervaka för din app

* Genomsnittligt arbetsminne
* Genomsnittlig svarstid
* CPU-tid
* Arbetsminne
* Begäranden

![övervaka app kan lösa HTTP-fel 502 Felaktig gateway-och 503 tjänsten ej tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Mer information finns i:

* [Övervaka appar i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Samla in data
#### <a name="use-the-diagnostics-tool"></a>Använda diagnostikverktyget för
App Service tillhandahåller en intelligent och interaktiv upplevelse för att hjälpa dig att felsöka din app med krävs ingen konfiguration. När du stöter på problem med din app, påpeka diagnostikverktyget fel att guida dig till rätt information för att enkelt och snabbt felsöka och lösa problemet.

Om du vill få åtkomst till App Service-diagnostik, navigera till din App Service-app eller en App Service Environment i den [Azure-portalen](https://portal.azure.com). I det vänstra navigeringsfönstret klickar du på **diagnostisera och lösa problem**.

#### <a name="use-the-kudu-debug-console"></a>Använda Kudu-Felsökningskonsolen
App Service levereras med ett Felsökningskonsolen som du kan använda för felsökning, utforska, ladda upp filer, samt JSON-slutpunkter för att hämta information om din miljö. Detta kallas den *Kudu-konsolen* eller *instrumentpanelen SCM* för din app.

Du kan komma åt den här instrumentpanelen genom att gå till länken **https://&lt;ditt appnamn >.scm.azurewebsites.net/**.

Några av de saker som Kudu tillhandahåller är:

* miljöinställningar för ditt program
* loggström
* diagnostiska dump
* Felsök konsolen där du kan köra Powershell-cmdlets och grundläggande DOS-kommandon.

En annan bra funktion i Kudu är att, om ditt program som utlöste första chansen-undantag, du kan använda Kudu och verktyget SysInternals Procdump skapa minne minnesdumpar. Dessa minnesdumpar är ögonblicksbilder av processen och ofta kan hjälpa dig att felsöka mer komplicerade problem med din app.

Läs mer om funktioner som är tillgängliga i Kudu [Azure Websites online-verktyg som du bör känna till om](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Åtgärda problemet
#### <a name="scale-the-app"></a>Skala appen
I Azure App Service kan för bättre prestanda och dataflöde, du justera skalan där du kör ditt program. Skala upp en app omfattar två relaterade åtgärder: Om du ändrar din App Service-plan till en högre prisnivå och konfigurera vissa inställningar när du har växlat till högre prisnivå.

Läs mer om att skala [skala en app i Azure App Service](web-sites-scale.md).

Dessutom kan du välja att köra programmet i fler än en instans. Detta inte bara får du fler funktioner för bearbetning, men ger dig även vissa delar av feltolerans. Om processen stängs av på en instans, fortsätter fortfarande den andra instansen begäranhantering.

Du kan ange skalning för att vara manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd AutoHeal
AutoHeal återanvänder arbetsprocessen för din app baserat på dina inställningar (till exempel konfigurationsändringar, begäranden, Minnesbaserad gränser eller den tid som behövs för att utföra en begäran). I de flesta fall, är omarbetning av processen det snabbaste sättet att komma tillrätta med problem. Även om du kan alltid starta om appen direkt i Azure Portal, gör AutoHeal det automatiskt åt dig. Allt du behöver göra är att lägga till vissa utlösare i rot web.config för din app. Observera att de här inställningarna skulle fungerar på samma sätt, även om ditt program inte är en .NET något.

Mer information finns i [automatisk återställning Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Starta om appen
Det här är ofta det enklaste sättet att återställa efter engångshändelser. På den [Azure-portalen](https://portal.azure.com/), appbladet, har du alternativ för att stoppa eller starta om din app.

 ![Starta om appen för att lösa HTTP-fel 502 Felaktig gateway-och 503 tjänsten ej tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Du kan också hantera din app med Azure Powershell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

