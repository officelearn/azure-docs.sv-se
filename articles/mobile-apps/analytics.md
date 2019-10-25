---
title: Förstå användningen av mobil program och användar beteende med Visual Studio App Center-och Azure-tjänster
description: Lär dig mer om tjänsterna som App Center som hjälper dig att fatta smarta affärs beslut genom att förstå hur användare använder ditt mobila program.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795801"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>Analysera och förstå användningen av mobila program
Hur bra är det att förstå hur dina användare använder dina program? Hur många aktiva användare har och hur används användnings ändringar över tid? Vilka funktioner använder de och vilka som används mest? Var är dessa användare baserade? Hur många användare använder den senaste versionen av programmet? Alla dessa frågor är viktiga att känna till för att göra din app till en lyckad verksamhet. Du måste samla in användnings data från dina appar för att kunna besvara dessa typer av användnings analys frågor.

Mer du tittar närmare på data, desto mer kan du hitta sätt att förbättra ditt program och hålla dina användare glada. Det är viktigt att använda data för att hitta åtgärds bara insikter och hålla användarna glad.

## <a name="importance-of-analytics"></a>Prioritet för analys
- **Förstå** dina användare, hur de interagerar med ditt program och gör det möjligt för dem att finjustera ditt program och tillhandahålla fantastiska upplevelser för att växa din verksamhet.
- **Spåra** användnings statistik för att fatta välgrundade beslut om hur du marknadsför ditt program och bättre betjänar din kund.
- **Mät** programmets prestanda.
- **Lär dig** vilka delar av ditt programs enhets värde och prestanda.
- **Data drivna insikter** om problem med omsättning och kvarhållning.

Använd följande tjänster för att aktivera analys av mobil program.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Med [App Center Analytics](/appcenter/analytics/) kan du utöka mål gruppen genom att fokusera på det som är viktigt, med djupgående rapportering och insikter om användarsessioner, främsta enheter, OS-versioner och beteende analyser. Skapa enkelt anpassade händelser för att spåra vad som helst med omfattande program analys.

   **Viktiga funktioner**
   - **Spåra** användnings mönster, användar antagande och andra uppdrags mått kostnads fritt.
   - **Identifiera** trender, användar beteende och engagemang genom anpassade händelser.
   - **Färdiga mått** och **detaljerade insikter** om program användning (varje dag, varje vecka, varje månad), sessioner, enhets egenskaper och användar demografiska på en **enda instrument panel**.
   - **Exportera alla dina analys data kontinuerligt till Azure** för obegränsad kvarhållning. Stöder export till Azure Blob Storage och Azure Application insikter.
   - **Integrera med Azure Application insikter** för ännu djupare insikter, till exempel kvarhållning, tratt-analys och kohorter
   - **En-rad SDK-integration** för att komma igång på några minuter.
   - **Plattforms stöd** – iOS, Android, MacOS, tvOS, Xamarin, reakta Native, Unity, Cordova.

   **Reference**
   - [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Kom igång med App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor innehåller [Application Insights](/azure/azure-monitor/app/app-insights-overview) som innehåller verktyg för att samla in och analysera telemetri för att maximera prestanda och övervaka ditt mobila program. Du kan dra nytta av Application Insights genom att använda App Center analys och konfigurera export till Application Insights. Application Insights kan fråga, segmentera, filtrera och analysera den anpassade händelse Telemetrin från dina program, utöver de analys verktyg App Center innehåller.

**Viktiga funktioner**
   - **Fråga** din anpassade händelse-telemetri.
   - **Filtrera** händelse-telemetri med kraftfulla segment funktioner.
   - **Analysera** konverterings-, bevarande-och navigerings mönster i ditt program.
     - **Trattar** som analyserar och övervakar konverteringsfrekvens.
     - **Kvarhållning** för analys av hur bra ditt program behåller användare över tid.
     - **Arbetsböcker** som kombinerar grafik och text i en rapport som kan delas.
     - **Kohorter** som namnger och sparar specifika användar- eller händelsegrupper så att det lätt går att hänvisa till dem från andra analysverktyg.

**Reference**
- [Azure-portalen](https://portal.azure.com/)
- [Analysera ditt mobil program med App Center och Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Användnings analys med Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure-PlayFab
[Azure PlayFab](https://playfab.com/) erbjuder en komplett Server dels plattform med spel tjänster, real tids analys och LiveOps som du behöver för att skapa molnbaserade spel i världs klass. De här tjänsterna minskar barriärerna för att lansera spel utvecklare, och erbjuder både stora och små Studios kostnads effektiva utvecklings lösningar som kan skalas med sina spel och hjälpa dem att engagera, behålla och förlita dig på spelare. PlayFab gör det möjligt för utvecklare att använda det intelligenta molnet för att bygga och hantera spel, analysera spel data och förbättra den övergripande spel upplevelsen.

**Viktiga funktioner**
   - **Övervaka** instrument paneler i real tid.
   - **Utvärdera** spelets prestanda via främsta mått.
   - **Rapport** för att granska sammanfattningar av ditt spels dagliga och månatliga prestanda via automatiskt genererade rapporter som kan visas i Game Manager och hämtas eller levereras till din inkorg dagligen.
   - **A/B-test** för att köra experiment och fastställa den optimala inställningen för en viss variabel.
   - **Segmentering** för spelare gör att du kan definiera automatiska grupperingar av spelare.
    
**Reference**
- [PlayFab-portalen](https://developer.playfab.com/en-US/sign-up)
- [Analys](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Snabbstarter](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
