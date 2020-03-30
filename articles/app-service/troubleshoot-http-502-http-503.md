---
title: Åtgärda HTTP 502- och HTTP 503-fel
description: Felsöka 502 felaktiga gateway- och 503-tjänstfel som inte är tillgängliga i appen som finns i Azure App Service.
tags: top-support-issue
keywords: 502 dålig gateway, 503 tjänst tillgänglig, fel 503, fel 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9345b6fb28aa282e85f1167f6f2531e5f990e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688330"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Felsöka HTTP-fel i "502 bad gateway" och "503-tjänsten är inte tillgänglig" i Azure App Service
"502 dålig gateway" och "503-tjänst är inte tillgänglig" är vanliga fel i din app som finns i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Den här artikeln hjälper dig att felsöka dessa fel.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Alternativt kan du också arkivera en Azure-supportincident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/) och klicka på **Hämta support**.

## <a name="symptom"></a>Symptom
När du bläddrar till appen returneras ett HTTP-fel i 502-fel eller ett HTTP-fel för 503-tjänsten som inte är tillgänglig.

## <a name="cause"></a>Orsak
Det här problemet orsakas ofta av problem på programnivå, till exempel:

* förfrågningar tar lång tid
* program med hög minne/CPU
* programmet kraschar på grund av ett undantag.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Felsökningssteg för att lösa fel i felen "502 felaktig gateway" och "503-tjänsten är inte tillgänglig"
Felsökning kan delas in i tre olika uppgifter, i sekventiell ordning:

1. [Observera och övervaka programbeteende](#observe)
2. [Samla in data](#collect)
3. [Minska problemet](#mitigate)

[App Service](overview.md) ger dig olika alternativ i varje steg.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observera och övervaka applikationsbeteende
#### <a name="track-service-health"></a>Spåra tjänstens hälsa
Microsoft Azure offentliggör varje gång det sker ett avbrott i tjänsten eller prestandaförsämring. Du kan spåra tjänstens hälsotillstånd på [Azure Portal](https://portal.azure.com/). Mer information finns i [Spåra tjänstens hälsotillstånd](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Övervaka din app
Med det här alternativet kan du ta reda på om ditt program har några problem. Klicka på panelen **Begäranden och fel** i appens blad. **Bladet Mått** visar alla mått som du kan lägga till.

Några av de mått som du kanske vill övervaka för din app är

* Genomsnittligt arbetsminne
* Genomsnittlig svarstid
* CPU-tid
* Arbetsminne
* Begäranden

![övervaka app för att lösa HTTP-fel på 502 dålig gateway och 503 tjänst inte tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Mer information finns i:

* [Övervaka appar i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Samla in uppgifter
#### <a name="use-the-diagnostics-tool"></a>Använda diagnostikverktyget
App Service ger en intelligent och interaktiv upplevelse som hjälper dig att felsöka din app utan att någon konfiguration krävs. När du stöter på problem med din app, visar diagnostikverktyget vad som är fel för att guida dig till rätt information för att enklare och snabbare felsöka och lösa problemet.

Om du vill komma åt App Service-diagnostik navigerar du till appens tjänstapp eller App Service-miljö i [Azure-portalen](https://portal.azure.com). I den vänstra navigeringen, klicka på **Diagnostisera och lösa problem**.

#### <a name="use-the-kudu-debug-console"></a>Använda Kudu-felsökningskonsolen
App Service levereras med en felsökningskonsol som du kan använda för felsökning, utforska, ladda upp filer samt JSON-slutpunkter för att få information om din miljö. Detta kallas *Kudu-konsolen* eller *SCM-instrumentpanelen* för din app.

Du kan komma åt den här instrumentpanelen genom att gå till länken **https://&lt;Ditt appnamn>.scm.azurewebsites.net/**.

Några av de saker som Kudu ger är:

* miljöinställningar för ditt program
* loggström
* diagnostisk dump
* felsökningskonsol där du kan köra Powershell-cmdlets och grundläggande DOS-kommandon.

En annan användbar funktion i Kudu är att om ditt program kastar första chansen undantag, kan du använda Kudu och SysInternals verktyget Procdump för att skapa minnesdumpar. Dessa minnesdumpar är ögonblicksbilder av processen och kan ofta hjälpa dig att felsöka mer komplicerade problem med din app.

Mer information om funktioner som är tillgängliga i Kudu finns i [Onlineverktyg för Azure Websites som du bör känna till](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Minska problemet
#### <a name="scale-the-app"></a>Skala appen
I Azure App Service, för ökad prestanda och dataflöde, kan du justera den skala som du kör ditt program. Att skala upp en app innebär två relaterade åtgärder: att ändra apptjänstplanen till en högre prisnivå och konfigurera vissa inställningar när du har växlat till den högre prisnivån.

Mer information om skalning finns [i Skala en app i Azure App Service](manage-scale-up.md).

Dessutom kan du välja att köra programmet på mer än en instans . Detta ger dig inte bara mer bearbetningskapacitet, men ger dig också en viss mängd feltolerans. Om processen går ner på en instans fortsätter den andra instansen fortfarande att betjäna begäranden.

Du kan ställa in skalningen som manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd AutoHeal
AutoHeal återvinner arbetsprocessen för din app baserat på inställningar du väljer (som konfigurationsändringar, begäranden, minnesbaserade gränser eller den tid som krävs för att utföra en begäran). För det mesta, återvinna processen är det snabbaste sättet att återhämta sig från ett problem. Även om du alltid kan starta om appen direkt i Azure Portal, gör AutoHeal det automatiskt för dig. Allt du behöver göra är att lägga till några utlösare i roten web.config för din app. Observera att dessa inställningar skulle fungera på samma sätt även om ditt program inte är en .NET en.

Mer information finns i Automatisk läkning av [Azure-webbplatser](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Starta om appen
Detta är ofta det enklaste sättet att återhämta sig från engångsfrågor. På [Azure Portal](https://portal.azure.com/)på appens blad har du möjlighet att stoppa eller starta om appen.

 ![starta om appen för att lösa HTTP-fel på 502 felaktig gateway och 503-tjänst är inte tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Du kan också hantera din app med Azure Powershell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

