---
title: Azure App konfigurations funktions hantering
description: En översikt över hur Azure App konfiguration kan användas för att aktivera och inaktivera program funktioner på begäran.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 48aebfe1ba6af348e98e5600075f3a9e9dce1a8e
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495770"
---
# <a name="feature-management-overview"></a>Översikt över funktions hantering

För att leverera en ny program funktion krävs en fullständig omdistribution av själva programmet. Om du vill testa en funktion måste du förmodligen distribuera programmet flera gånger för att kontrol lera när funktionen blir synlig eller vem som ska kunna se den.

Funktions hantering är en modern program utvecklings metod som frigör funktions lansering från kod distribution och gör det möjligt att snabbt ändra funktionsens tillgänglighet på begäran. Den använder en teknik som kallas *funktions flaggor* (kallas även för *funktions växlingar*, *funktions växlar*och så vidare) för att dynamiskt administrera en funktions livs cykel.

Funktions hantering hjälper utvecklare att hantera följande problem:

* **Kod förgrenings hantering**: funktions flaggor används ofta för att figursätta nya program funktioner som är under utveckling. Sådan funktionalitet är "dold" som standard. Du kan på ett säkert sätt leverera funktionen, även om den är oavslutad och den förblir vilande i produktionen. Genom att använda den här metoden, som kallas för *mörk distribution*, kan du släppa all kod i slutet av varje utvecklings cykel. Du behöver inte längre ha någon kod gren över flera cykler på grund av att en funktion som tar fler än en cykel slutförs.
* **Testa i produktion**: du kan använda funktions flaggor för att ge tidig till gång till nya funktioner i produktionen. Du kan till exempel begränsa åtkomsten till endast dina grupp medlemmar eller till interna beta testare. De här användarna får hela produktions miljön i stället för en simulerad eller delvis upplevelse i en test miljö.
* **Flygning**: du kan också använda funktions flaggor för att stegvis distribuera nya funktioner till slutanvändarna. Du kan rikta in dig på en liten procent andel av din användar population först och öka procent andelen gradvis över tid, efter att du har fått förtroende för implementeringen.
* **Omedelbar Kill-växel**: funktions flaggor utgörs av ett säkerhets nät som gör det möjligt att lansera nya funktioner. Med dem kan du snabbt aktivera och inaktivera program funktioner. Om det behövs kan du snabbt inaktivera en funktion utan att behöva bygga om och omdistribuera ditt program.
* **Selektiv aktivering**: även om de flesta funktions flaggor bara finns tills deras associerade funktioner har släppts, kan vissa efter en längre tid vara. Du kan använda funktions flaggor för att segmentera dina användare och leverera en speciell uppsättning funktioner till varje grupp. Du kan till exempel ha en funktion som bara fungerar i en viss webbläsare. Du kan definiera en funktions flagga så att endast användare av webbläsaren kan se och använda funktionen. Med den här metoden kan du enkelt expandera listan över webbläsare som stöds senare utan att behöva göra några kod ändringar.

## <a name="basic-concepts"></a>Grundläggande begrepp

Här följer flera nya villkor som rör funktions hantering:

* **Funktions flagga**: en funktions flagga är en variabel med en binär status *på* eller *av*. Funktions flaggan har också ett associerat kodblock. Funktions flaggans tillstånd utlöser om kod blocket körs eller inte.
* **Funktions hanteraren**: en funktions hanterare är ett programpaket som hanterar livs cykeln för alla funktions flaggor i ett program. Funktions hanteraren innehåller vanligt vis ytterligare funktioner, till exempel cachelagring av funktions flaggor och uppdatering av deras tillstånd.
* **Filter**: ett filter är en regel för att utvärdera status för en funktions flagga. En användar grupp, en enhet eller en webb läsar typ, en geografisk plats och ett tidsfönster är exempel på vad ett filter kan representera.

En effektiv implementering av funktions hantering består av minst två komponenter som arbetar i samförstånd:

* Ett program som använder funktions flaggor.
* En separat lagrings plats som lagrar funktions flaggorna och deras aktuella tillstånd.

Hur dessa komponenter interagerar illustreras i följande exempel.

## <a name="feature-flag-usage-in-code"></a>Funktions flagga användning i kod

Det grundläggande mönstret för att implementera funktions flaggor i ett program är enkelt. Du kan tänka på en funktions flagga som en boolesk tillstånd-variabel som används med en `if` villkorlig instruktion i koden:

```csharp
if (featureFlag) {
    // Run the following code
}
```

I detta fall körs det omslutna kod blocket om `featureFlag` är inställt på `True`. annars hoppas den över. Du kan ange värdet för `featureFlag` statiskt, som i följande kod exempel:

```csharp
bool featureFlag = true;
```

Du kan också utvärdera flaggans tillstånd baserat på vissa regler:

```csharp
bool featureFlag = isBetaUser();
```

Ett något mer komplicerat mönster för funktions flagga inkluderar även en `else`-instruktion:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Du kan dock skriva om det här beteendet i Basic-mönstret. [Använd funktions flaggor i en ASP.net Core app](./use-feature-flags-dotnet-core.md) som visar fördelarna med att standardisera ett enkelt kod mönster. Ett exempel:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Databas för funktions flagga

Om du vill använda funktions flaggor effektivt måste du Externalize alla funktions flaggor som används i ett program. Med den här metoden kan du ändra funktions flagga tillstånd utan att ändra och omdistribuera själva programmet.

Azure App-konfigurationen är utformad som en central lagrings plats för funktions flaggor. Du kan använda den för att definiera olika typer av funktions flaggor och ändra deras tillstånd snabbt och säkert. Du kan sedan använda konfigurations biblioteken för appen för olika programmerings språk ramverk för att enkelt komma åt dessa funktions flaggor från ditt program.

[Använd funktions flaggor i en ASP.net Core app](./use-feature-flags-dotnet-core.md) som visar hur biblioteken för .net Core-appens konfiguration och funktions hanterings bibliotek används tillsammans för att implementera funktions flaggor för ASP.NET-webbprogrammet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till funktions flaggor i en ASP.NET Core-webbapp](./quickstart-feature-flag-aspnet-core.md)  
