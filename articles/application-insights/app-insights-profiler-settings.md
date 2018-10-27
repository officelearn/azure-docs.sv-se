---
title: Azure Application Insights Profiler inställningsbladet | Microsoft Docs
description: Se profiler status och starta profilering av sessioner
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 81608dd7281ceddce7e0701535ad99e1c9e44315
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142506"
---
# <a name="configure-application-insights-profiler"></a>Konfigurera Application Insights Profiler

## <a name="profiler-settings-page"></a>Sidan för inställningar av Profiler

Inställningssidan profiler kan öppnas från sidan Application Insights prestanda genom att trycka på den **Profiler** knappen.

![konfigurera profiler fönstret post][configure-profiler-entry]

Sidan Konfigurera Application Insights Profiler innehåller fyra funktioner: 
1. **Profilera nu** – med den här knappen kommer orsaka profilering sessioner att starta för alla appar som är länkade till den här instansen av Application Insights
1. **Länkade appar** -lista över program som skickar profiler till den här Application Insights-resurs
1. **Sessioner pågår** – när du trycker på **profil nu**, status för sessionen visas här)
1. **Senaste profilering sessioner** – visar information om de senaste profilering sessioner.

![Profiler på begäran][profiler-on-demand]

## <a name="app-service-environments-ase"></a>App Service-miljöer (ASE)
Beroende på hur din ASE konfigurerad, kan vara blockerad anropet för att kontrollera agentstatusen. Den här sidan står det att agenten inte körs när den är i själva verket. Du kan kontrollera webbjobbet programmet noga. Men om alla inställningar är korrekta och App Insights-webbplatstillägg är installerad på ditt program, profiler körs och om det finns tillräcklig trafik till ditt program bör du se senaste profilering sessioner i listan.

## <a id="profileondemand"></a> Utlös Profiler manuellt

Profiler kan aktiveras manuellt med ett enda musklick. Anta att du kör ett webbtest för prestanda. Du måste spårningarna för att hjälpa dig att förstå hur ditt webbprogram fungerar under belastning. Det är viktigt att ha kontroll över när spårningssessioner samlas eftersom du vet när belastningstestet ska köras, men slumpmässiga exempelintervallet kan gå miste om den.
Följande steg illustrerar hur det här scenariot fungerar:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Valfritt) Steg 1: Skapa trafik till webbappen genom att starta ett webbtest för prestanda

Om din webbapp redan har inkommande trafik, eller om du bara vill generera manuellt trafik, hoppa över det här avsnittet och fortsätter du till steg 2.

Gå till Application Insights-portalen **konfigurera > prestandatestning**. Klicka på ny om du vill starta ett nytt prestandatest.

![Skapa nytt prestandatest][create-performance-test]

I den **nytt prestandatest** fönstret Konfigurera mål-URL för testning. Acceptera alla standardinställningar och börja köra belastningstestet.

![Konfigurera belastningstest][configure-performance-test]

Nytt test är först i kön, följt av statusen ”Pågående” visas.

![Läs in test har skickats och väntar i kö][load-test-queued]

![belastningstest körs pågår][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Steg 2: Börja profiler på begäran

När belastningstestet körs kan börja vi profiler för att fånga in spårningar på webbappen medan den tar emot belastningen.
Gå till fönstret Konfigurera Profiler:


### <a name="step-3-view-traces"></a>Steg 3: Visa spår

Följ anvisningarna på meddelandet för att gå till sidan och visa prestandaspårningar när profiler är klar.

## <a name="troubleshooting-on-demand-profiler"></a>Felsökning av profiler för på begäran

Ibland kan du se Profiler timeout-felmeddelande efter en på begäran-session:

![Profiler tidsgränsfel][profiler-timeout]

Det kan finnas två orsaker till varför du ser det här felet:

1. På begäran profiler sessionen är klar men Application Insights tog längre tid att bearbeta insamlade data. Om data inte avslutades bearbetas på 15 minuter, visas ett timeout-meddelande i portalen. Även om efter ett tag visas Profiler-spårningar. Om detta inträffar kan ignorera felmeddelandet för tillfället. Vi arbetar på att lösa.

1. Webbappen har en äldre version av Profiler-agent som inte har funktionen på begäran. Om du har aktiverat Application Insights-profilen tidigare litar du behöver uppdatera dina Profiler-agent ska starta med hjälp av funktionen på begäran.
  
Följ stegen nedan för att kontrollera och installera den senaste Profiler:

1. Gå till Appinställningar för App Services och kontrollera om följande inställningar är inställda:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersätt med rätt instrumenteringsnyckeln för Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 om någon av dessa inställningar inte, gå till fönstret Application Insights aktivering att installera den senaste webbplatstillägg.

1. Gå till Application Insights-fönstret i App Services-portalen.

    ![Aktivera Application Insights från App Services-portalen][enable-app-insights]

1. Om en ”uppdatera”-knappen på följande sida visas klickar du på den för att uppdatera Application Insights-webbplatstillägg som ska installera den senaste versionen Profiler-agenten.

    ![Uppdatera webbplatstillägg][update-site-extension]

1. Klicka sedan på **ändra** till att se till att Profiler är på och välj **OK** att spara ändringarna.

    ![Ändra och spara appinsikter][change-and-save-appinsights]

1. Gå tillbaka till **Appinställningar** fliken för den App Service för att kontrollera följande för app-inställningar är inställda:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersätt med rätt instrumenteringsnyckeln för application insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![appinställningar för profiler][app-settings-for-profiler]

1. Du kan också kontrollera tilläggsversion och se till att ingen uppdatering är tillgänglig.

    ![Sök efter uppdatering av webbappstillägget][check-for-extension-update]

## <a name="next-steps"></a>Nästa steg
[Aktivera Profiler och visa spår](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png