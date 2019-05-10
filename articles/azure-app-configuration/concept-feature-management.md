---
title: Azure App funktionen konfigurationshantering | Microsoft Docs
description: En översikt över hur konfiguration av Azure kan användas för att aktivera eller inaktivera programmet funktioner på begäran
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
ms.openlocfilehash: 304ca08275ccf4bf32d38e3fc89dd5cf07460fa4
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413773"
---
# <a name="feature-management"></a>Funktionshantering

Traditionellt levererade en ny funktion i programmet kräver en fullständig Omdistributionen av själva programmet. Om du vill testa en funktion, har du troligen att distribuera ditt program många gånger kontroll när den blir synlig eller vilka som får se den. Hantering av funktionen är en modern software development idé som frikopplar funktionsutgåva från koddistribution och möjliggör snabba ändringar till tillgängliga funktioner på begäran. Den använder en teknik som kallas *funktionen flaggan* (även kallat *funktionen växla*, *bpe*och så vidare) att administrera en funktion livscykel dynamiskt. Det gör att utvecklare kan hantera följande problem:

* **Code förgreningshantering**: Flaggor för funktionen används ofta för att omsluta nya funktioner i programmet som är under utveckling. Sådan funktion dolda ”” som standard. Du kan på ett säkert sätt skickar den, även om de hunnit och förblir inaktiva i produktion. Med hjälp av den här metoden kallas *mörk distribution*, du kan släppa all kod i slutet av varje utvecklingscykel. Du behöver inte längre att bibehålla valfri kod gren för flera cykler på grund av en funktion som tar mer än en period ska slutföras.
* **Testa i produktion**: Du kan använda funktionen flaggor för att ge snabb åtkomst till nya funktioner i produktion. Du kan exempelvis begränsa åtkomst till dina gruppmedlemmar eller intern betatestare. Dessa användare kommer att få fullständig exakthet produktion-upplevelsen i stället för en simulerad eller partiella upplevelse i en testmiljö.
* **Förhandsversionstestning**: Du kan också använda funktionen flaggor lansera nya funktioner för slutanvändare inkrementellt. Du kan rikta en liten andel av dina användarpopulationen först och öka den gradvis med tiden, när du har fått tryggare i implementeringen.
* **Omedelbar kill växel**: Flaggor för funktionen tillhandahåller en inbyggd säkerhetsnät för lanserar nya funktioner. Med dem, kan du inte bara aktivera utan också stänga av programfunktioner lätt. Du får möjlighet att snabbt kunna inaktivera en funktion, om det behövs utan att behöva återskapa och distribuera programmet igen.
* **Selektiv aktivering**: Även om de flesta flaggor för funktionen finns bara tills deras associerade funktioner har släppts har räcker några under en längre tid. Du kan använda dem som ett sätt att dela in användarna och leverera en specifik uppsättning funktioner i varje grupp. Du kan till exempel ha en funktion som fungerar på en viss webbläsare. Du kan definiera en funktionsflagga så att endast användare av den webbläsaren kan se och använda funktionen. Fördelen med den här metoden är att du kan enkelt expandera listan över webbläsare som stöds senare utan att behöva göra någon kod ändra.

## <a name="basic-concepts"></a>Grundläggande begrepp

Här följer flera nya termer som rör hantering av funktionen.

* **Funktionen flaggan**: En funktionsflagga är en variabel med en binär tillstånd *på* eller *av* och har en associerad kodblock. Tillståndet beslutar om kodblocket körs eller inte.
* **Funktionen manager**: En funktion manager är ett programpaket som hanterar livscykeln för alla funktionen flaggor i ett program. Den innehåller normalt ytterligare funktioner, till exempel cachelagring av flaggor och uppdatering av deras tillstånd.
* **Filtret**: Ett filter är en regel för att utvärdera tillståndet för en funktionsflagga. Användargrupp, enhet eller webbläsare typ, geoplats, tidsperioden är alla exempel på vad ett filter kan representera.

En effektiv implementering av hantering av funktionen består av minst två komponenter som arbetar tillsammans: ett program som gör användningen av funktionen flaggor och en separat databas som lagrar flaggor för funktionen och deras aktuella tillstånd. Deras interaktioner visas nedan.

## <a name="feature-flag-usage-in-code"></a>Flagga för användning av funktioner i kod

Det grundläggande mönstret för att implementera funktionen flaggor i ett program är enkelt. Du kan få en överblick den som en variabel för booleska tillstånd som används med en `if` villkorlig instruktionen i din kod.

```csharp
if (featureFlag) {
    // Run the following code
}
```

I det här fallet om `featureFlag` är inställd på `True`, inom klammerparenteserna kodblocket körs; annars hoppas den över. Värdet för `featureFlag` kan ställas in statiskt:

```csharp
bool featureFlag = true;
```

Den kan också utvärderas baserat på regler:

```csharp
bool featureFlag = isBetaUser();
```

En lite mer komplicerat funktionen flaggan mönstret omfattar ett `else` instruktionen samt.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Detta kan skrivas i det grundläggande mönstret men. [Använder funktionen flaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md) visar fördelen med att standardisera på ett från en enkel kod.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Funktionen flaggan lagringsplats

Om du vill använda dem på ett effektivt sätt, måste du gör alla funktionen flaggor som används i ett program. Som du kan ändra flaggan funktionsstatus utan att ändra och distribuera om själva programmet. Konfiguration av Azure är avsett att vara en centraliserad databas för funktionen flaggor. Du kan använda den för att definiera olika typer av flaggor för funktionen och ändra deras tillstånd snabbt och säkert. Du kan sedan komma åt dessa flaggor för funktionen enkelt från ditt program med Appkonfiguration-bibliotek för olika programmeringsspråk språkramverken. [Använder funktionen flaggor i en ASP.NET Core-app](./use-feature-flags-dotnet-core.md) visar hur konfigurationsprovider för .NET Core-App och funktionen hanteringsbibliotek används tillsammans som en komplett lösning för att implementera funktionen flaggor för ASP.NET-webbprogram.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till funktionen flaggor i en ASP.NET Core-webbapp](./quickstart-feature-flag-aspnet-core.md)  
