---
title: Nyckelvärdeslagring för Azure App Configuration | Microsoft Docs
description: En översikt över hur konfigurationsdata lagras i Azure-Appkonfiguration
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
ms.openlocfilehash: 39367cbe6c001fc782fd899ee3a99b37ece70a77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741197"
---
# <a name="key-value-store"></a>Nyckelvärdeslagring

Konfiguration av Azure lagrar konfigurationsdata som nyckel / värde-par. Nyckel / värde-par är ett enkelt men flexibelt sätt som representerar olika typer av programinställningar som utvecklare är bekant med.

## <a name="keys"></a>Nycklar

Nycklar fungerar som namn för nyckel / värde-par och används för att lagra och hämta motsvarande värden. Det är vanligt att organisera nycklar i ett hierarkiskt namnområde med hjälp av en avgränsaren, till exempel `/` eller `:`. Använd en konvention som passar bäst för ditt program. App Configuration behandlar nycklar som en helhet. Den parsa inte nycklar för att ta reda på hur deras namn är strukturerade eller framtvinga Standardregeln på dem.

Användningen av konfiguration av lagra inom ramverk för programmet kan avgöra specifika filnamnen för nyckelvärden. Till exempel Java's Spring Cloud-ramverket definierar `Environment` resurser som skicka inställningar till en Spring-program till parameteriseras via variabler som omfattar *programnamn* och *profil*. Nycklar för Spring Cloud-relaterade konfigurationsdata börjar vanligtvis med dessa två element avgränsade med en avgränsare.

Nycklar som lagras i App Configuration är skiftlägeskänsliga, Unicode-baserade strängar. Nycklarna *app1* och *App1* skiljer sig i ett appkonfigurationsarkiv. Tänk på detta när du använder konfigurationsinställningar i ett program, eftersom vissa ramverk hantera konfigurationsnycklar case-insensitively. Till exempel behandlar konfigurationssystemet för ASP.NET Core nycklar som icke-skiftlägeskänsliga strängar. För att undvika oväntade beteenden när du frågar Appkonfiguration i ett ASP.NET Core-program kan inte använda nycklar som endast skiljer skiftläge.

Du kan använda valfria unicode-tecken i nyckelnamn som angetts i konfiguration av undantag för `*`, `,`, och `\`. Följande tecken är reserverade. Om du vill inkludera ett reserverat tecken måste du hoppa över det med hjälp av `\{Reserved Character}`. Det finns en kombinerad storleksgräns på 10 000 tecken på ett nyckel / värde-par. Den här gränsen gäller alla tecken i nyckeln, dess värde och alla tillhörande valfria attribut. Inom den här gränsen kan du ha många hierarkiska nivåer för nycklar.

### <a name="design-key-namespaces"></a>Utforma nyckeln namnområden

Det finns två generella metoder för namngivning av nycklar som används för konfigurationsdata: platt eller hierarkisk. Dessa metoder är liknande ur ett programs synvinkel för användning, men hierarkisk naming erbjuder flera fördelar:

* Enklare att läsa. I stället för en lång sekvens av tecken fungerar avgränsare i en hierarkisk nyckelnamn som blanksteg i en mening. Du får även naturlig radbrytningar mellan ord.
* Enklare att hantera. En nyckelnamnshierarki representerar logiska grupper med konfigurationsdata.
* Enklare att använda. Det är enklare att skriva en fråga som mönstermatchar nycklar i en hierarkisk struktur och bara hämtar en del av konfigurationsdata. Dessutom har många nyare programming ramverk inbyggt stöd för hierarkisk konfigurationsdata så att ditt program kan göra användning av specifika uppsättningar av konfiguration.

Du kan organisera nycklar i App Configuration hierarkiskt på många sätt. Se sådana nycklar som [URI: er](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Varje hierarkisk nyckel är en resurs *sökväg* består av en eller flera komponenter som är sammankopplade med avgränsare. Välj vilka tecken som ska användas som avgränsare baserat på vilka dina program, programmeringsspråk eller framework behov. Använda flera avgränsare för olika nycklar i Appkonfiguration.

Här är flera exempel på hur du kan strukturera nyckelnamnen i en hierarki:

* Baserat på komponenttjänster

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Baserat på distributionsregioner

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Etikett-nycklar

Nyckelvärden i konfiguration av kan du kan också ha en etikettattribut. Etiketter för att skilja mellan värden med samma nyckel. En nyckel *app1* med etiketter *A* och *B* utgör två separata nycklar i en appbutik för konfigurationen. Som standard ett nyckelvärde etikett är tomt, eller `null`.

Etiketten är ett enkelt sätt att skapa varianter av en nyckel. Ett vanligt användningsområde för etiketter är att ange flera miljöer för samma nyckel:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Version nyckelvärden

Konfiguration av inte automatiskt version nyckelvärden som de har ändrats. Använda etiketter som ett sätt att skapa flera versioner av ett nyckelvärde. Exempelvis kan du ange ett versionsnummer för programmet eller Git genomförande-ID i etiketter att identifiera viktiga värden som är associerade med en viss programvara-version.

Du kan använda valfria unicode-tecken i etiketter utom för `*`, `,`, och `\`. Följande tecken är reserverade. För att inkludera ett reserverat tecken, måste du undanta den med hjälp av `\{Reserved Character}`.

### <a name="query-key-values"></a>Fråga nyckelvärden

Varje nyckelvärdet identifieras unikt genom dess nyckel plus en etikett som kan vara `null`. Du kan fråga en appbutik konfiguration för värden genom att ange ett mönster. Konfigurationsarkivets app returnerar alla nyckelvärden som matchar mönstret och deras motsvarande värden och attribut. Använd följande viktiga mönster i REST API-anrop till Appkonfiguration:

| Nyckel | |
|---|---|
| `key` utelämnas eller `key=*` | Matchar alla nycklar |
| `key=abc` | Nyckelnamn för matchningar **abc** exakt |
| `key=abc*` | Matchar nyckelnamn som börjar med **abc** |
| `key=*abc` | Matchar nyckelnamn som slutar med **abc** |
| `key=*abc*` | Matchar nyckelnamn som innehåller **abc** |
| `key=abc,xyz` | Matchar nyckeln namn **abc** eller **xyz**begränsat till fem CSV: er |

Du kan även inkludera följande etikett mönster:

| Label (Etikett) | |
|---|---|
| `label` utelämnas eller `label=*` | Matchar alla, vilket innefattar `null` |
| `label=%00` | Matchningar `null` etikett |
| `label=1.0.0` | Matchar etiketten **1.0.0** exakt |
| `label=1.0.*` | Matchar etiketter som börjar med **1.0.** |
| `label=*.0.0` | Matchar etiketter som slutar med **.0.0** |
| `label=*.0.*` | Matchar etiketter som innehåller **.0.** |
| `label=%00,1.0.0` | Matchar etiketter `null` eller **1.0.1**begränsat till fem CSV: er |

## <a name="values"></a>Värden

Värden som tilldelas till nycklar är också Unicode-strängar. Du kan använda alla Unicode-tecken för värden. Det finns en valfri användardefinierad innehållstyp som är associerade med varje värde. Använd det här attributet för att lagra information, till exempel en kodningsschema om ett värde som bidrar till att dina program att behandla den korrekt.

Som lagras i en appbutik för konfigurationen som innehåller alla nycklar och värden, krypteras i vila och under överföring. Konfiguration av är inte en lösning för ersättning för Azure Key Vault. Inte lagra programhemligheter i den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Point-in-time-ögonblicksbild](./concept-point-time-snapshot.md)  
