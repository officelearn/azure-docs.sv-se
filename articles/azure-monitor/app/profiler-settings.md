---
title: Använda Azure Application Insights Profiler inställningsfönstret | Microsoft Docs
description: Se Profiler status och starta profilering av sessioner
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 9603c45443c6339a127f977600eeff2ba57a283f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228240"
---
# <a name="configure-application-insights-profiler"></a>Konfigurera Application Insights Profiler

## <a name="profiler-settings-pane"></a>Profiler inställningsfönstret

Gå till fönstret Application Insights prestanda för att öppna fönstret Azure Application Insights Profiler inställningar och välj sedan den **Profiler** knappen.

![Konfigurera fönstret Profiler][configure-profiler-entry]

Den **konfigurera Application Insights Profiler** fönstret innehåller fyra funktioner: 
* **Profilera nu**: Startar profilering sessioner för alla appar som är länkade till den här instansen av Application Insights.
* **Länkade appar**: Visar en lista över program som skickar profilering data till den här Application Insights-resursen.
* **Sessioner pågår**: Visar status för sessionen när du väljer **profil nu**. 
* **Senaste profilering sessioner**: Visar information om de senaste profilering sessioner.

![Profiler på begäran][profiler-on-demand]

## <a name="app-service-environment"></a>App Service Environment
Beroende på hur Azure App Service Environment konfigureras blockeras anropet för att kontrollera agentstatusen. Fönstret kanske visas ett meddelande om att agenten inte körs även när den körs. För att säkerställa att den är, kontrollera webbjobbet på ditt program. Om alla värden för app-inställningarna är korrekta och Application Insights-webbplatstillägg är installerad på ditt program, körs Profiler. Om ditt program tar emot tillräckligt med trafik, ska senaste profilering sessioner visas i en lista.

## <a id="profileondemand"></a> Utlös Profiler manuellt

### <a name="minimum-requirements"></a>Minimikrav 
En användare kan utlösa en profiler session manuellt kräver de minst ”skriva” åtkomst på deras roll för Application Insights-komponent. I de flesta fall du får den här åtkomsten automatiskt och inget ytterligare arbete krävs. Om du har problem med är rollen prenumeration omfång för att lägga till rollen ”Application Insights Component Contributor”. [Mer information om rollen åtkomstkontroll med Azure Monitoring](https://docs.microsoft.com/en-us/azure/azure-monitor/app/resources-roles-access-control).

Du kan utlösa Profiler manuellt med ett enda klick. Anta att du kör ett webbtest för prestanda. Du måste spårningarna för att hjälpa dig att förstå hur ditt webbprogram fungerar under belastning. Att ha kontroll över när spårningssessioner samlas är avgörande, eftersom du vet när belastningstestet ska köras. Men slumpmässiga exempelintervallet kan gå miste om den.

I nästa avsnitt visar hur det här scenariot fungerar:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Steg 1: (Valfritt) Generera trafik till webbappen genom att starta ett webbtest för prestanda

Om din webbapp redan har inkommande trafik, eller om du bara vill generera manuellt trafik, hoppa över det här avsnittet och fortsätter du till steg 2.

1. I Application Insights-portalen väljer du **konfigurera** > **prestandatestning**. 

1. Om du vill starta ett nytt prestandatest, Välj den **New** knappen.

   ![Skapa nytt prestandatest][create-performance-test]

1. I den **nytt prestandatest** fönstret Konfigurera mål-URL för testning. Acceptera alla standardinställningar och välj sedan **Kör test** för start av belastningstestet.

    ![Konfigurera belastningstest][configure-performance-test]

    Nytt test är först i kön, följt av statusen *pågår*.

    ![Läs in test har skickats och väntar i kö][load-test-queued]

    ![belastningstest körs pågår][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Steg 2: Starta en session för på begäran av Profiler

1. När belastningstestet körs, startar du Profiler för att fånga in spårningar på webbappen medan den tar emot belastningen.

1. Gå till den **konfigurera Profiler** fönstret.


### <a name="step-3-view-traces"></a>Steg 3: Visa spår

Följ anvisningarna på meddelandet för att gå till fönstret och visa prestandaspårningar när Profiler är klar.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Felsökningsåtgärd Profiler på begäran

Du kan få ett felmeddelande för Profiler timeout efter en på begäran-session:

![Profiler tidsgränsfel][profiler-timeout]

Du kan få det här felet efter någon av följande orsaker:

* Profiler-session på begäran lyckades men Application Insights tog längre tid än förväntat att bearbeta insamlade data.  

  Om data inte bearbetas inom 15 minuter, visar portalen ett timeout-meddelande. Efter ett tag, men visas Profiler-spårningar. Om du får ett felmeddelande, Ignorera. Vi arbetar på att lösa.

* Webbappen har en äldre version av Profiler-agent som inte har funktionen på begäran.  

  Om du har aktiverat Application Insights Profiler tidigare kan du behöva uppdatera dina Profiler-agent ska starta med hjälp av funktionen på begäran.
  
Gå till App Services **Appinställningar** fönstret och Sök efter följande inställningar:
* **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersätt med rätt instrumenteringsnyckeln för Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Om något av föregående värden inte har angetts kan du installera den senaste webbplatstillägg genom att göra följande:

1. Gå till den **Application Insights** fönstret på den App Services-portalen.

    ![Aktivera Application Insights från App Services-portalen][enable-app-insights]

1. Om den **Programinsikter** fönstret visar en **uppdatera** knappen, väljer du alternativet att uppdatera Application Insights-webbplatstillägg som ska installera den senaste versionen Profiler-agenten.

    ![Uppdatera webbplatstillägg][update-site-extension]

1. För att säkerställa att Profiler är aktiverad, Välj **ändra**, och välj sedan **OK** att spara ändringarna.

    ![Ändra och spara appinsikter][change-and-save-appinsights]

1. Gå tillbaka till **Appinställningar** för App Service att kontrollera att följande värden är:
   * **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersätt med rätt instrumenteringsnyckeln för application insights.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![Appinställningar för Profiler][app-settings-for-profiler]

1. Alternativt kan du välja **tillägg**, och kontrollera vilken tilläggsversion och avgöra om en uppdatering är tillgänglig.

    ![Sök efter uppdatering av webbappstillägget][check-for-extension-update]

## <a name="next-steps"></a>Nästa steg
[Aktivera Profiler och visa spår](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
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
