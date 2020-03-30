---
title: Förstå användning av mobila program och användarbeteende med Visual Studio App Center och Azure-tjänster
description: Läs mer om tjänster som App Center som hjälper dig att fatta smarta affärsbeslut genom att förstå hur användarna använder ditt mobilapplikation.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241085"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analysera och förstå användning av mobilappar
Hur väl förstår du hur användarna använder dina program? Hur många aktiva användare har ditt program och hur förändras användningen med tiden? Vilka funktioner använder de, och vilka används mest? Var är dessa användare baserade? Hur många användare använder den senaste versionen av programmet? Alla dessa frågor är viktiga att förstå för att göra din app till ett framgångsrikt företag. För att besvara den typen av frågor om användningsanalys måste du samla in användningsdata från dina appar.

Ju mer du tittar ner i data, desto mer kan du hitta sätt att förbättra ditt program och hålla dina användare nöjda. Det är viktigt att använda data för att hitta användbara insikter och hålla användarna nöjda.

## <a name="importance-of-analytics"></a>Betydelsen av analys
- Förstå dina användare, hur de interagerar med ditt program och vad som för dem tillbaka för att finjustera ditt program och ge bra upplevelser för att få ditt företag att växa.
- Spåra dina användningsmått för att fatta välgrundade beslut om hur du marknadsför ditt program och bättre betjäna dina kunder.
- Mät programmets prestanda.
- Ta reda på vilka delar av programmets enhetsvärde och prestanda.
- Få datadrivna insikter i problem som rör omsättning och kvarhållning.

Använd följande tjänster för att aktivera mobilprogramanalys.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Med App Center Analytics](/appcenter/analytics/) kan du öka målgruppen genom att fokusera på det som är viktigt. Det ger djup rapportering och insikter om användarsessioner, toppenheter, OS-versioner och beteendeanalys. Skapa enkelt anpassade händelser för att spåra allt med omfattande programanalyser.

   **Huvudfunktioner**
   - Spåra användningsmönster, användaranvändning och andra engagemangsmått utan kostnad.
   - Identifiera trender, användarbeteende och engagemang genom anpassade händelser.
   - Hämta färdiga mått och detaljerade insikter om programanvändning (dagligen, veckovis, månadsvis), sessioner, enhetsegenskaper och användardemografi i en enda instrumentpanel.
   - Exportera kontinuerligt alla Dina App Center Analytics-data till Azure för obegränsad kvarhållning. App Center Analytics stöder export till Azure Blob storage och Azure Application Insights.
   - Integrera med Azure Application Insights för ännu djupare insikter, till exempel kvarhållning, trattanalys och kohorter.
   - Använd enrads SDK-integrering för att komma igång inom några minuter.
   - Få plattformsstöd för iOS, Android, macOS, tvOS, Xamarin, React Native, Unity och Cordova.

   **Referenser**
   - [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Komma igång med App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor innehåller [Application Insights](/azure/azure-monitor/app/app-insights-overview), som innehåller verktyg för att samla in och analysera telemetri för att maximera prestanda och övervaka ditt mobila program. Du kan dra nytta av Application Insights genom att använda App Center Analytics för att konfigurera export till Application Insights. Application Insights kan fråga, segmentera, filtrera och analysera den anpassade händelsetelemetrin från dina program, utöver de analysverktyg som App Center tillhandahåller.

**Huvudfunktioner**
   - Fråga den anpassade händelsetelemetrin.
   - Filtrera händelsetelemetri med kraftfulla segmenteringsfunktioner.
   - Analysera konverterings-, kvarhållnings- och navigeringsmönster i ditt program. Du kan använda:
     - Trattar för att analysera och övervaka omräkningskurser.
     - Kvarhållning för att analysera hur väl ditt program behåller användare över tid.
     - Arbetsböcker för att kombinera visualiseringar och text till en delbar rapport.
     - Kohorter för att namnge och spara specifika grupper av användare eller händelser så att de enkelt kan refereras från andra analysverktyg.

**Referenser**
- [Azure-portal](https://portal.azure.com/)
- [Analysera din mobilapplikation med App Center och Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Använda App Center Analytics med application insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) erbjuder en komplett backend-plattform med speltjänster, realtidsanalys och LiveOps som du behöver för att skapa molnanslutna spel i världsklass. Dessa tjänster minskar hindren för lansering för spelutvecklare. De erbjuder både stora och små studior kostnadseffektiva utvecklingslösningar som skalar med sina spel. Tjänsterna kan hjälpa studior engagera, behålla och tjäna pengar på spelare. Med PlayFab kan utvecklare använda det intelligenta molnet för att bygga och driva spel, analysera speldata och förbättra de övergripande spelupplevelserna.

**Huvudfunktioner**
   - Övervaka instrumentpaneler i realtid.
   - Utvärdera spelets resultat genom toppmått.
   - Granska sammanfattningar av spelets dagliga och månatliga resultat genom automatiskt skapade rapporter. Du kan visa rapporterna i Game Manager och få dem nedladdade eller levererade till inkorgen dagligen.
   - Använd A/B-testning för att köra experiment och bestämma den optimala inställningen för en viss variabel.
   - Använd segmentering för spelare för att definiera automatiserade grupperingar av spelare.
    
**Referenser**
- [PlayFab-portal](https://developer.playfab.com/en-US/sign-up)
- [Analys](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Snabbstarter](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
