---
title: Diagnostik och lösningsverktyg
description: Lär dig hur du kan felsöka problem med din app i Azure App Service med diagnostik och lösningsverktyg i Azure-portalen.
keywords: apptjänst, azure app-tjänst, diagnostik, support, webbapp, felsökning, självhjälp
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: df8f68e47776f46ae2d1331f85bd76d6cfe17b80
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869950"
---
# <a name="azure-app-service-diagnostics-overview"></a>Översikt över Diagnostik i Azure App Service

När du kör ett webbprogram vill du vara förberedd på eventuella problem som kan uppstå, från 500 fel till användarna som talar om att din webbplats är nere. App Service diagnostik är en intelligent och interaktiv upplevelse som hjälper dig att felsöka din app utan konfiguration krävs. När du stöter på problem med din app påpekar App Service-diagnostik vad som är fel för att guida dig till rätt information för att enklare och snabbare felsöka och lösa problemet.

Även om den här upplevelsen är till stor hjälp när du har problem med din app under de senaste 24 timmarna, är alla diagnostiska diagram alltid tillgängliga för dig att analysera.

App Service diagnostik fungerar för inte bara din app på Windows, men även appar på [Linux / behållare](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), App Service [Environment](https://docs.microsoft.com/azure/app-service/environment/intro)och [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Öppna diagnostik för apptjänst

Om du vill komma åt App Service-diagnostik navigerar du till din App Service-webbapp eller App Service-miljö i [Azure-portalen](https://portal.azure.com). I den vänstra navigeringen, klicka på **Diagnostisera och lösa problem**.

För Azure Functions navigerar du till din funktionsapp och i den övre navigeringen klickar du på **Plattformsfunktioner**och väljer **Diagnostisera och lösa problem** i avsnittet **Resurshantering.**

På startsidan för App Service-diagnostik kan du välja den kategori som bäst beskriver problemet med din app med hjälp av sökorden på varje startsida. Den här sidan hittar du även **diagnostikverktyg** för Windows-appar. Se [Diagnostikverktyg (endast för Windows-app)](#diagnostic-tools-only-for-windows-app).

![Startsida](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Om appen är nere eller går långsamt kan du [samla in en profileringsspårning](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) för att identifiera orsaken till problemet. Profilering är låg vikt och är utformad för produktionsscenarier.
>

## <a name="interactive-interface"></a>Interaktivt gränssnitt

När du har valt en startsidas kategori som bäst stämmer överens med appens problem kan App Service-diagnostikens interaktiva gränssnitt, Genie, guida dig genom att diagnostisera och lösa problem med din app. Du kan använda panelgenvägarna som tillhandahålls av Genie för att visa den fullständiga diagnostikrapporten för den problemkategori som du är intresserad av. Panelgenvägarna ger dig ett direkt sätt att komma åt dina diagnostikmått.

![Genvägar på paneler](./media/app-service-diagnostics/tile-shortcuts-2.png)

När du har klickat på dessa paneler kan du se en lista över ämnen som är relaterade till problemet som beskrivs i panelen. Dessa avsnitt innehåller utdrag med anmärkningsvärd information från den fullständiga rapporten. Du kan klicka på något av dessa ämnen för att undersöka frågorna ytterligare. Du kan också klicka på **Visa fullständig rapport för** att utforska alla ämnen på en enda sida.

![Ämnen](./media/app-service-diagnostics/application-logs-insights-3.png)

![Visa hela rapporten](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostikrapport

När du har valt att undersöka problemet ytterligare genom att klicka på ett ämne kan du visa mer information om ämnet som ofta kompletteras med diagram och markdowns. Diagnostikrapport kan vara ett kraftfullt verktyg för att identifiera problemet med din app.

![Diagnostikrapport](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Hälsokontroll

Om du inte vet vad som är fel med din app eller inte vet var du ska börja felsöka dina problem, är hälsokontrollen ett bra ställe att börja på. Hälsokontrollen analyserar dina program för att ge dig en snabb, interaktiv översikt som pekar ut vad som är hälsosamt och vad som är fel, och talar om var du ska leta för att undersöka problemet. Dess intelligenta och interaktiva gränssnitt ger dig vägledning genom felsökningsprocessen. Hälsokontrollen är integrerad med Genie-upplevelsen för Windows-appar och diagnostikrapporten för webbappar för Linux-appar.

### <a name="health-checkup-graphs"></a>Diagram för hälsokontroll

Det finns fyra olika diagram i hälsokontrollen.

- **begäranden och fel:** Ett diagram som visar antalet begäranden som gjorts under de senaste 24 timmarna tillsammans med HTTP-serverfel.
- **appens prestanda:** Ett diagram som visar svarstid under de senaste 24 timmarna för olika percentilgrupper.
- **CPU-användning:** Ett diagram som visar den totala procent cpu-användningen per instans under de senaste 24 timmarna.  
- **minnesanvändning:** Ett diagram som visar den totala procentuella fysiska minnesanvändningen per instans under de senaste 24 timmarna.

![Hälsokontroll](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Undersöka problem med programkod (endast för Windows-appar)

Eftersom många appproblem är relaterade till problem i programkoden integreras App Service-diagnostik med [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) för att markera undantag och beroendeproblem som korrelerar med det valda driftstoppet. Application Insights måste aktiveras separat.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Om du vill visa undantag och beroenden för Application Insights markerar du **webbappen nedåt** eller **webbappens långsamma** panelgenvägar.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Felsökningssteg (endast för Windows-appar)

Om ett problem upptäcks med en viss problemkategori under de senaste 24 timmarna kan du visa den fullständiga diagnostikrapporten, och App Service-diagnostik kan uppmana dig att visa fler felsökningsråd och nästa steg för en mer guidad upplevelse.

![Programinsikter och felsökning och nästa steg](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostikverktyg (endast för Windows-appar)

Diagnostikverktyg innehåller mer avancerade diagnostikverktyg som hjälper dig att undersöka programkodproblem, långsamhet, anslutningssträngar med mera. och proaktiva verktyg som hjälper dig att minska problem med CPU-användning, begäranden och minne.

### <a name="proactive-cpu-monitoring"></a>Proaktiv CPU-övervakning

Proaktiv CPU-övervakning ger dig ett enkelt och proaktivt sätt att vidta en åtgärd när din app eller underordnade process för din app förbrukar höga CPU-resurser. Du kan ställa in dina egna CPU tröskelregler för att tillfälligt minska en hög CPU-problem tills den verkliga orsaken till det oväntade problemet hittas. Mer information finns i [Minska dina processorproblem innan de inträffar](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proaktiv CPU-övervakning](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Automatisk läkning och proaktiv automatisk läkning

Automatisk läkning är en begränsningsåtgärd som du kan vidta när appen har oväntat beteende. Du kan ange egna regler baserat på antal begäranden, långsam begäran, minnesgräns och HTTP-statuskod för att utlösa begränsningsåtgärder. Använd verktyget för att tillfälligt minska ett oväntat beteende tills du hittar grundorsaken. Mer information finns [i Presentera den nya automatiska läkningsupplevelsen i apptjänstdiagnostik](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Automatisk läkning](./media/app-service-diagnostics/auto-healing-10.png)

Precis som proaktiv CPU-övervakning är proaktiv automatisk läkning en nyckelfärdig lösning för att minska oväntade beteenden i din app. Proaktiv automatisk läkning startar om appen när App Service fastställer att appen inte kan återupptäckeas. Mer information finns i [Introduktion till Proaktiv automatisk läkaning](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigator och ändringsanalys (endast för Windows-app)

I ett stort team med kontinuerlig integrering och där din app har många beroenden kan det vara svårt att identifiera den specifika ändring som orsakar ett felaktigt beteende. Navigator hjälper till att få synlighet på appens topologi genom att automatiskt återge en beroendekarta över din app och alla resurser i samma prenumeration. Med Navigator kan du visa en konsoliderad lista över ändringar som gjorts av din app och dess beroenden och begränsa en ändring som orsakar felbeteende. Den kan nås via startsidan kakel **Navigator** och måste aktiveras innan du använder den första gången. Mer information finns [i Få insyn i appens beroenden med Navigator](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Standardsida för navigator](./media/app-service-diagnostics/navigator-default-page-11.png)

![Diff-vy](./media/app-service-diagnostics/diff-view-12.png)

Ändringsanalys för appändringar kan nås via panelgenvägar, **programändringar** och **programkrascher** i **tillgänglighet och prestanda** så att du kan använda den samtidigt med andra mått. Innan du använder funktionen måste du först aktivera den. Mer information finns [i Presentera den nya förändringsanalysupplevelsen i App Service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Skicka dina frågor eller feedback på [UserVoice](https://feedback.azure.com/forums/169385-web-apps) genom att lägga till "[Diag]" i titeln.
