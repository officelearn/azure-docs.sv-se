---
title: Azure App funktionen konfigurationshantering | Microsoft Docs
description: En översikt över hur konfiguration av Azure kan användas för att aktivera eller inaktivera programmet funktioner på begäran.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393366"
---
# <a name="feature-management-overview"></a>Översikt över funktionen

Traditionellt har kräver levererade en ny funktion i programmet en fullständig Omdistributionen av själva programmet. Om du vill testa en funktion, måste du förmodligen distribuera ditt program många gånger till kontroll när funktionen blir synlig eller vilka som får se den.

Hantering av funktionen är en modern programutveckling idé som frikopplar funktionsutgåva från koddistribution och möjliggör snabba ändringar till tillgängliga funktioner på begäran. Den använder en teknik som kallas *funktionen flaggor* (även kallat *funktionen växlar*, *funktionsväxlarna*och så vidare) att dynamiskt administrera livscykeln för en funktion.

Funktionen hantering hjälper utvecklare att hantera följande problem:

* **Code förgreningshantering**: Flaggor för funktionen används ofta för att omsluta nya funktioner i programmet som är under utveckling. Sådan funktion dolda ”” som standard. Du kan på ett säkert sätt skickar funktionen, även om den har hunnit, och den förblir inaktiva i produktion. Med den här metoden kan anropas *mörk distribution*, du kan släppa all kod i slutet av varje utvecklingscykel. Du behöver inte längre att bibehålla valfri kod gren för flera cykler på grund av en funktion som tar mer än en cykel för att slutföra.
* **Testa i produktion**: Du kan använda funktionen flaggor för att ge snabb åtkomst till nya funktioner i produktion. Du kan exempelvis begränsa åtkomsten till endast dina gruppmedlemmar eller intern betatestare. Dessa användare kommer att få fullständig exakthet produktion-upplevelsen i stället för en simulerad eller partiella upplevelse i en testmiljö.
* **Förhandsversionstestning**: Du kan också använda funktionen flaggor stegvis lansera nya funktioner för slutanvändare. Du kan rikta en liten andel av dina användarpopulationen först och öka den gradvis med tiden, när du har fått tryggare i implementeringen.
* **Omedelbar kill växel**: Flaggor för funktionen tillhandahåller en inbyggd säkerhetsnät för lanserar nya funktioner. Med dem, kan du lätt stänga programfunktioner och inaktivera. Om det behövs kan du snabbt inaktivera en funktion utan att bygga om och omdistribuera ditt program.
* **Selektiv aktivering**: Även om de flesta flaggor för funktionen finns bara tills deras associerade funktioner har släppts har räcker några under en längre tid. Du kan använda funktionen flaggor för att dela in användarna och leverera en specifik uppsättning funktioner i varje grupp. Du kan till exempel ha en funktion som fungerar bara på en viss webbläsare. Du kan definiera en funktionsflagga så att endast användare av den webbläsaren kan se och använda funktionen. Med den här metoden kan expandera du enkelt listan över webbläsare som stöds senare utan att behöva göra några ändringar i koden.

## <a name="basic-concepts"></a>Grundläggande begrepp

Här följer flera nya termer som rör hantering av funktionen:

* **Funktionen flaggan**: En funktionsflagga är en variabel med en binär tillstånd *på* eller *av*. Funktionsflagga har även en associerad kodblock. Tillståndet för funktionsflagga utlöser om kodblocket körs eller inte.
* **Funktionen manager**: En funktion manager är ett programpaket som hanterar livscykeln för funktionen flaggor i ett program. Funktionen manager innehåller normalt ytterligare funktioner, till exempel cachelagring flaggor för funktionen och uppdatera deras tillstånd.
* **Filtret**: Ett filter är en regel för att utvärdera tillståndet för en funktionsflagga. Är alla exempel på vad ett filter kan representera en användargrupp, en enhet eller webbläsare typ, en geografisk plats och ett tidsintervall.

En effektiv implementering av hantering av funktionen består av minst två komponenter som arbetar tillsammans:

* Ett program som gör användning av funktionen flaggor.
* En separat databas som lagrar flaggor för funktionen och deras aktuella tillstånd.

Hur de här komponenterna samverkar illustreras i följande exempel.

## <a name="feature-flag-usage-in-code"></a>Flagga för användning av funktioner i kod

Det grundläggande mönstret för att implementera funktionen flaggor i ett program är enkelt. Du kan se en funktionsflagga som en boolesk tillstånd-variabeln som används med en `if` villkorlig instruktionen i din kod:

```csharp
if (featureFlag) {
    // Run the following code
}
```

I det här fallet om `featureFlag` är inställd på `True`, inom klammerparenteserna kodblocket är utförda, annars, hoppas den över. Du kan ange värdet för `featureFlag` statiskt, som i följande kodexempel:

```csharp
bool featureFlag = true;
```

Du kan även utvärdera den flaggan tillstånd baserat på regler:

```csharp
bool featureFlag = isBetaUser();
```

En lite mer komplicerat funktionen flaggan mönstret omfattar ett `else` samt instruktionen:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Du kan skriva om det här beteendet i det grundläggande mönstret men. [Använder funktionen flaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md) visar fördelarna med att standardisera på ett från en enkel kod. Exempel:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Funktionen flaggan lagringsplats

Du vill använda funktionen flaggor effektivt gör alla funktionen flaggor som används i ett program. Den här metoden kan du ändra flaggan funktionsstatus utan att ändra och distribuera om själva programmet.

Konfiguration av Azure är avsett att vara en centraliserad databas för funktionen flaggor. Du kan använda den för att definiera olika typer av flaggor för funktionen och ändra deras tillstånd snabbt och säkert. Du kan sedan använda Appkonfiguration-bibliotek för olika programmeringsspråk språkramverken för att enkelt komma åt dessa flaggor för funktionen från ditt program.

[Använder funktionen flaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md) visar hur konfigurationsprovider för .NET Core-App och funktionen hanteringsbibliotek används tillsammans att implementera funktionen flaggor för ASP.NET-webbprogram.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till funktionen flaggor i en ASP.NET Core-webbapp](./quickstart-feature-flag-aspnet-core.md)  
