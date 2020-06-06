---
title: Förstå användningen av mobil program och användar beteende med Visual Studio App Center-och Azure-tjänster
description: Lär dig mer om tjänsterna som App Center som hjälper dig att fatta smarta affärs beslut genom att förstå hur användare använder ditt mobila program.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ebe07ec76e5b852dbe7d030ad8859d59ce5cd074
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451062"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analysera och förstå användning av mobil program
Hur bra är det att förstå hur dina användare använder dina program? Hur många aktiva användare har ditt program och hur ändras användningen över tid? Vilka funktioner använder de och vilka som används mest? Var är dessa användare baserade? Hur många användare använder den senaste versionen av programmet? Alla dessa frågor är viktiga att känna till för att göra din app till en lyckad verksamhet. Du måste samla in användnings data från dina appar för att kunna besvara dessa typer av användnings analys frågor.

Mer du tittar närmare på data, desto mer kan du se hur du kan förbättra ditt program och se till att användarna är nöjda. Det är viktigt att använda data för att hitta åtgärds bara insikter och hålla användare uppfyllda.

## <a name="importance-of-analytics"></a>Prioritet för analys
- Förstå dina användare, hur de interagerar med ditt program och gör det möjligt för dem att finjustera ditt program och tillhandahålla fantastiska upplevelser för att växa din verksamhet.
- Spåra användnings statistik för att fatta välgrundade beslut om hur du marknadsför ditt program och bättre betjänar dina kunder.
- Mät programmets prestanda.
- Lär dig vilka delar av ditt programs enhets värde och prestanda.
- Få data drivna insikter om problem som rör omsättning och kvarhållning.

Använd följande tjänster för att aktivera analys av mobil program.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Med [App Center Analytics](/appcenter/analytics/) kan du utöka mål gruppen genom att fokusera på det som är viktigt. Den erbjuder djupgående rapporter och insikter om användarsessioner, främsta enheter, OS-versioner och beteende analys. Skapa enkelt anpassade händelser för att spåra vad som helst med omfattande program analys.

   **Huvudfunktioner**
   - Spåra användnings mönster, användar antagande och andra uppdrags mått kostnads fritt.
   - Identifiera trender, användar beteende och engagemang genom anpassade händelser.
   - Få färdiga Mät värden och detaljerade insikter om program användning (varje dag, varje vecka, varje månad), sessioner, enhets egenskaper och användar demografiska data på en enda instrument panel.
   - Exportera alltid alla App Center Analytics-data till Azure för obegränsad kvarhållning. App Center Analytics stöder export till Azure Blob Storage och Azure Application insikter.
   - Integrera med Azure Application insikter för ännu djupare insikter, till exempel kvarhållning, tratt-analys och kohorter.
   - Kom igång på några minuter med hjälp av en SDK-integrering med en rad.
   - Få plattforms stöd för iOS, Android, macOS, tvOS, Xamarin, reagera inbyggd, Unity och Cordova.

   **Referenser**
   - [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Kom igång med App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor innehåller [Application Insights](/azure/azure-monitor/app/app-insights-overview)som innehåller verktyg för att samla in och analysera telemetri för att maximera prestanda och övervaka ditt mobila program. Du kan dra nytta av Application Insights genom att använda App Center analys för att konfigurera export till Application Insights. Application Insights kan fråga, segmentera, filtrera och analysera anpassad händelse telemetri från dina program, utöver de analys verktyg som App Center tillhandahåller.

**Huvudfunktioner**
   - Fråga den anpassade händelsetelemetrin.
   - Filtrera händelse-telemetri med kraftfulla segment funktioner.
   - Analysera konverterings-, bevarande-och navigerings mönster i ditt program. Du kan använda:
     - Trattar för att analysera och övervaka konverterings takt.
     - Kvarhållning för att analysera hur bra ditt program behåller användare över tid.
     - Arbets böcker som kombinerar visualiseringar och text till en delnings bara rapport.
     - Kohorter för att namnge och spara vissa grupper av användare eller händelser så att de enkelt kan refereras från andra analys verktyg.

**Referenser**
- [Azure Portal](https://portal.azure.com/)
- [Analysera ditt mobil program med App Center och Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Använda App Center Analytics med Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) erbjuder en komplett Server dels plattform med spel tjänster, real tids analys och LiveOps som du behöver för att skapa molnbaserade spel i världs klass. Dessa tjänster minskar barriärerna för att lansera spel utvecklare. De erbjuder både stora och små Studios kostnads effektiva utvecklings lösningar som skalar med sina spel. Tjänsterna kan hjälpa Studios att engagera, behålla och tjäna spelare. Med PlayFab kan utvecklare använda det intelligenta molnet för att bygga och hantera spel, analysera spel data och förbättra övergripande spel upplevelser.

**Huvudfunktioner**
   - Övervaka instrument paneler i real tid.
   - Utvärdera spelets prestanda via främsta mått.
   - Granska sammanfattningar av spelets dagliga och månatliga prestanda genom autogenererade rapporter. Du kan visa rapporterna i Game Manager och låta dem hämtas eller levereras till din inkorg dagligen.
   - Använd ett/B-test för att köra experiment och fastställa den optimala inställningen för en viss variabel.
   - Använd segmentering för spelare för att definiera automatiska grupperingar av spelare.
    
**Referenser**
- [PlayFab-portalen](https://developer.playfab.com/en-US/sign-up)
- [Analys](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Snabbstarter](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
