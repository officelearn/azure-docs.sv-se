---
title: Förstå funktionshantering med Azure App-konfiguration
description: Aktivera och inaktivera funktioner med Azure App-konfiguration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523738"
---
# <a name="feature-management-overview"></a>Översikt över funktionshantering

Traditionellt kräver leverans av en ny programfunktion en fullständig omfördelning av själva programmet. Att testa en funktion kräver ofta flera distributioner av programmet.  Varje distribution kan ändra funktionen eller exponera funktionen för olika kunder för testning.  

Funktionshantering är en modern programvara utveckling praxis som frikopplar funktionen release från koddistribution och möjliggör snabba ändringar av funktionen tillgänglighet på begäran. Den använder en teknik som kallas *funktionsflaggor* (kallas även *funktionsväxlingar,* *funktionsväxlar*och så vidare) för att dynamiskt administrera en funktions livscykel.

Funktionshantering hjälper utvecklare att lösa följande problem:

* **Kodgrenshantering**: Använd funktionsflaggor för att radbrytas nya programfunktioner som för närvarande är under utveckling. Sådana funktioner är "dolda" som standard. Du kan säkert skicka funktionen, även om det är oavslutat, och det kommer att förbli vilande i produktionen. Med den här metoden, som kallas *mörk distribution,* kan du släppa all din kod i slutet av varje utvecklingscykel. Du behöver inte längre underhålla kodgrenar över flera utvecklingscykler eftersom en viss funktion kräver mer än en cykel för att slutföra.
* **Test i produktion**: Använd funktionsflaggor för att ge tidig åtkomst till nya funktioner i produktionen. Du kan till exempel begränsa åtkomsten till gruppmedlemmar eller till interna betatestare. Dessa användare kommer att uppleva produktionen med full återgivning i stället för en simulerad eller partiell upplevelse i en testmiljö.
* **Flighting**: Använd funktionsflaggor för att stegvis distribuera nya funktioner till slutanvändare. Du kan rikta in dig på en liten andel av användarpopulationen först och öka den procentsatsen gradvis över tid.
* **Instant kill switch**: Funktionsflaggor ger ett ingående skyddsnät för att frigöra nya funktioner. Du kan aktivera och inaktivera programfunktioner utan att distribuera någon kod. Om det behövs kan du snabbt inaktivera en funktion utan att återskapa och distribuera om programmet.
* **Selektiv aktivering**: Använd funktionsflaggor för att segmentera användarna och leverera en specifik uppsättning funktioner till varje grupp. Du kanske har en funktion som bara fungerar i en viss webbläsare. Du kan definiera en funktionsflagga så att endast användare i den webbläsaren kan se och använda funktionen. Med den här metoden kan du enkelt utöka den webbläsarlista som stöds senare utan att behöva göra några kodändringar.

## <a name="basic-concepts"></a>Grundläggande begrepp

Här är flera nya termer relaterade till funktionshantering:

* **Funktionsflagga**: En funktionsflagga är en variabel med ett binärt tillstånd *för på* eller *av*. Funktionsflaggan har också ett associerat kodblock. Funktionsflaggans tillstånd utlöser om kodblocket körs.
* **Funktionshanteraren:** En funktionshanterare är ett programpaket som hanterar livscykeln för alla funktionsflaggor i ett program. Funktionshanteraren tillhandahåller också ytterligare funktioner, inklusive cachelagringsfunktionsflaggor och uppdatering av deras tillstånd.
* **Filter**: Ett filter är en regel för att utvärdera tillståndet för en funktionsflagga. Potentiella filter omfattar användargrupper, enhets- eller webbläsartyper, geografiska platser och tidsfönster.

Ett effektivt genomförande av funktionshantering består av minst två komponenter som arbetar i samförstånd:

* Ett program som använder funktionsflaggor.
* En separat databas som lagrar funktionsflaggorna och deras aktuella tillstånd.

## <a name="using-feature-flags-in-your-code"></a>Använda funktionsflaggor i koden

Det grundläggande mönstret för att implementera funktionsflaggor i ett program är enkelt. En funktionsflagga är en boolesk tillståndsvariabel som styr en villkorssats i koden:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Du kan ställa `featureFlag` in värdet statiskt.

```csharp
bool featureFlag = true;
```

Du kan utvärdera flaggans tillstånd baserat på vissa regler:

```csharp
bool featureFlag = isBetaUser();
```

Du kan utöka villkorsläget för att ange programbeteende för något av tillstånden:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Databas för funktionsflagga

Om du vill använda funktionsflaggor effektivt måste du externalisera alla funktionsflaggor som används i ett program. På så sätt kan du ändra funktionsflaggans lägen utan att ändra och distribuera om själva programmet.

Azure App Configuration tillhandahåller en centraliserad databas för funktionsflaggor. Du kan använda den för att definiera olika typer av funktionsflaggor och manipulera deras tillstånd snabbt och säkert. Du kan sedan använda appkonfigurationsbiblioteken för olika programmeringsspråkramverk för att enkelt komma åt dessa funktionsflaggor från ditt program.

[Använd funktionsflaggor i en ASP.NET Core-appen](./use-feature-flags-dotnet-core.md) visar hur .NET Core App Configuration provider och Funktionshanteringsbibliotek används tillsammans för att implementera funktionsflaggor för ditt ASP.NET webbprogram.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till funktionsflaggor i en ASP.NET Core-webbapp](./quickstart-feature-flag-aspnet-core.md)  
