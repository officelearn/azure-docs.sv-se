---
title: Använd fönstret Profilere för Azure Application Insights Profiler | Microsoft-dokument
description: Se Profilera status och starta profileringssessioner
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671638"
---
# <a name="configure-application-insights-profiler"></a>Konfigurera profiler för programstatistik

## <a name="updated-profiler-agent"></a>Uppdaterad Profiler agent
Utlösarfunktionerna fungerar bara med version 2.6 eller nyare av profileragenten. Om du kör en Azure App Service uppdateras din agent automatiskt. Du kan se vilken version av agenten du kör om du går till Kudu-URL:en för din https://yourwebsite.scm.azurewebsites.net/diagnosticserviceswebbplats och lägger till \DiagnosticServices till slutet av den, så här: . Application Insights Profiler Webjob ska version 2.6 eller nyare. Du kan tvinga fram en uppgradering genom att starta om webbappen. 

Om du kör profileren på en virtuell dator eller molntjänst måste du ha Windows Azure Diagnostics (WAD) tilläggsversion 16.0.4 eller nyare installerad. Du kan kontrollera versionen av WAD genom att logga in på den virtuella datorn och titta på den här katalogen: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. Katalognamnet är den version av WAD som är installerad. Azure VM-agenten uppdaterar WAD automatiskt när nya versioner är tillgängliga.

## <a name="profiler-settings-page"></a>Sidan Profilera inställningar

Om du vill öppna fönstret Profilerar profiler för Azure Application Insights går du till fönstret Prestanda för Application Insights och väljer sedan knappen **Konfigurera profiler.**

![Länk för att öppna sidan Profilerinställningar][configure-profiler-entry]

Det öppnar en sida som ser ut så här:

![Sidan Profilera inställningar][configure-profiler-page]

Sidan **Konfigurera programstatistik profilerar** dessa funktioner:

| | |
|-|-|
Profil nu | Startar profileringssessioner för alla appar som är länkade till den här instansen av Application Insights.
Utlösare | Gör att du kan konfigurera utlösare som gör att profileren körs. 
Senaste profileringssessioner | Visar information om tidigare profileringssessioner.

## <a name="profile-now"></a>Profil nu
Med det här alternativet kan du starta en profileringssession på begäran. När du klickar på den här länken börjar alla profileragenter som skickar data till den här Application Insights-instansen att samla in en profil. Efter 5 till 10 minuter visas profilsessionen i listan nedan.

För att en användare manuellt ska kunna utlösa en profilsession kräver de minst "skrivåtkomst" på sin roll för application insights-komponenten. I de flesta fall får du den här åtkomsten automatiskt och inget ytterligare arbete behövs. Om du har problem är rollen prenumerationsomfattning att lägga till rollen "Application Insights Component Contributor". [Läs mer om rollåtkomstkontroll med Azure Monitoring](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Inställningar för utlösare
![Utfällbara utfällbara triggerinställningar][trigger-settings-flyout]

Om du klickar på knappen Utlösare på menyraden öppnas rutan utlösarinställningar. Du kan ställa in utlösaren för att börja profilera när procentandelen CPU eller Minne använder träffar den nivå du anger.

| | |
|-|-|
På/ av-knapp | På: profiler kan startas av den här utlösaren; Av: profiler startar inte av den här utlösaren.
Minneströskel | När den här procentandelen minne används startas profileren.
Varaktighet | Anger hur länge profileraren ska köras när den utlöses.
Cooldown | Ställer in hur länge profileraren väntar innan du söker efter minne eller CPU-användning igen när den har utlösts.

## <a name="recent-profiling-sessions"></a>Senaste profilering sessioner
I det här avsnittet på sidan visas information om de senaste profileringssessionerna. En profileringssession representerar den tidsperiod då profileragenten tog en profil på en av de datorer som är värdar för ditt program. Du kan öppna profilerna från en session genom att klicka på en av raderna. För varje session visar vi:

| | |
|-|-|
Utlöses av | Hur sessionen startades, antingen av en utlösare, Profil nu eller standardprovtagning. 
Appnamn | Namnet på programmet som har profileras.
Maskininstans | Namnet på den maskin som profileragenten körde på.
Tidsstämpel | Tid då profilen fångades.
Tracee (spåre) | Antal spårningar som var kopplade till enskilda begäranden.
CPU i % | Procentandel processor som användes när profileraren kördes.
Minne % | Procentandel minne som användes när profileraren kördes.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>Använda webbprestandatester för att generera trafik till ditt program

Du kan utlösa Profiler manuellt med ett enda klick. Anta att du kör ett webbprestandatest. Du behöver spårningar som hjälper dig att förstå hur webbappen körs under belastning. Att ha kontroll över när spår fångas är avgörande, eftersom du vet när belastningstestet körs. Men det slumpmässiga samplingsintervallet kan missa det.

Nästa avsnitt visar hur det här scenariot fungerar:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Steg 1: Generera trafik till webbappen genom att starta ett webbprestandatest

Om webbappen redan har inkommande trafik eller om du bara vill generera trafik manuellt hoppar du över det här avsnittet och fortsätter till steg 2.

1. I application insights-portalen väljer du **Konfigurera** > **prestandatestning**. 

1. Om du vill starta ett nytt prestandatest väljer du knappen **Nytt.**

   ![skapa nytt prestandatest][create-performance-test]

1. Konfigurera testmål-URL:en i fönstret **Nytt prestandatest.** Acceptera alla standardinställningar och välj sedan **Kör test** för att börja köra belastningstestet.

    ![Konfigurera belastningstest][configure-performance-test]

    Det nya testet ställs först i kö, följt av status *för pågående*.

    ![Belastningstestet skickas och köas][load-test-queued]

    ![Belastningstest pågår][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Steg 2: Starta en profilsession på begäran

1. När belastningstestet körs startar du Profiler för att samla in spårningar i webbappen medan den tar emot belastning.

1. Gå till fönstret **Konfigurera profiler.**


### <a name="step-3-view-traces"></a>Steg 3: Visa spår

När Profiler har körts klart följer du anvisningarna för meddelande om att gå till fönstret Prestanda och visa spårningar.

## <a name="next-steps"></a>Nästa steg
[Aktivera profiler och visa spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
