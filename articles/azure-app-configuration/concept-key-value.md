---
title: Förstå Azure App Configuration nyckelvärdesbutik
description: Förstå hur konfigurationsdata lagras i Azure App-konfiguration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523612"
---
# <a name="keys-and-values"></a>Nycklar och värden

Azure App Configuration lagrar konfigurationsdata som nyckel-värde-par. Nyckel-värde par är en enkel och flexibel representation av programinställningar som används av utvecklare.

## <a name="keys"></a>Nycklar

Nycklar fungerar som identifierare för nyckelvärdespar och används för att lagra och hämta motsvarande värden. Det är vanligt att ordna nycklar i ett hierarkiskt namnområde med `/` hjälp `:`av en teckenavgränsare, till exempel eller . Använd en konvention som passar bäst för ditt program. App Configuration behandlar nycklar som en helhet. Det tolkar inte nycklar för att ta reda på hur deras namn är strukturerade eller genomdriva någon regel på dem.

Här är två exempel på nyckelnamn som är strukturerade i en hierarki:

* Baserat på komponenttjänster

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Baserat på distributionsregioner

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

Användningen av konfigurationsdata inom programramverk kan diktera specifika namngivningsscheman för nyckelvärden. Javas Spring Cloud-ramverk definierar `Environment` till exempel resurser som tillhandahåller inställningar till ett Spring-program.  Dessa är parameteriserade av variabler som innehåller *programnamn* och *profil*. Nycklar för Spring Cloud-relaterade konfigurationsdata börjar vanligtvis med dessa två element åtskilda av en avgränsare.

Nycklar som lagras i App Configuration är skiftlägeskänsliga, Unicode-baserade strängar. Nycklarna *app1* och *App1* är distinkta i en App Configuration Store. Tänk på detta när du använder konfigurationsinställningar i ett program eftersom vissa ramverk hanterar konfigurationsnycklar skiftlägesoberoende. Vi rekommenderar inte att du använder fodralet för att skilja nycklar.

Du kan använda vilket unicode-tecken `*` `,`som `\`helst i nyckelnamn utom , och .  Om du behöver inkludera ett av dessa reserverade tecken kan du escape det med hjälp `\{Reserved Character}`av . 

Det finns en sammanlagd storleksgräns på 10 kB på ett nyckelvärdespar. Den här gränsen innehåller alla tecken i nyckeln, dess värde och alla associerade valfria attribut. Inom den här gränsen kan du ha många hierarkiska nivåer för nycklar.

### <a name="design-key-namespaces"></a>Namnområden för designnyckel

Det finns två generella metoder för namngivning av nycklar som används för konfigurationsdata: platt eller hierarkisk. Dessa metoder är liknande från ett program användning synvinkel, men hierarkiska namngivning erbjuder ett antal fördelar:

* Enklare att läsa. Avgränsare i ett hierarkiskt nyckelnamn fungerar som blanksteg i en mening. De ger också naturliga pauser mellan ord.
* Enklare att hantera. En nyckelnamnshierarki representerar logiska grupper med konfigurationsdata.
* Enklare att använda. Det är enklare att skriva en fråga som mönstermatchar nycklar i en hierarkisk struktur och bara hämtar en del av konfigurationsdata. Dessutom har många nyare programmeringsramverk inbyggt stöd för hierarkiska konfigurationsdata så att ditt program kan använda specifika konfigurationsuppsättningar.

Du kan organisera nycklar i App Configuration hierarkiskt på många sätt. Tänk på sådana tangenter som [URI: er](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Varje hierarkisk nyckel är en *resurssökväg* som består av en eller flera komponenter som sammanfogas av avgränsare. Välj vilken karaktär som ska användas som avgränsare baserat på vad ditt program, programmeringsspråk eller ramverk behöver. Använd flera avgränsare för olika nycklar i Appkonfiguration.

### <a name="label-keys"></a>Etiketttangenter

Nyckelvärden i appkonfiguration kan eventuellt ha ett etikettattribut. Etiketter används för att skilja nyckelvärden med samma nyckel. En *nyckelapp1* med etiketterna *A* och *B* bildar två separata nycklar i ett App Configuration Store. Som standard har ett nyckelvärde ingen etikett. Om du uttryckligen vill referera till `\0` ett nyckelvärde `%00`utan etikett använder du (URL-kodad som ).

Label är ett bekvämt sätt att skapa varianter av en nyckel. En vanlig användning av etiketter är att ange flera miljöer för samma nyckel:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Värden för versionsnyckel

Appkonfigurationen versions inte nyckelvärden automatiskt. Använd etiketter som ett sätt att skapa flera versioner av ett nyckelvärde. Du kan till exempel ange ett programversionsnummer eller ett Git commit ID i etiketter för att identifiera nyckelvärden som är associerade med en viss programvaruversion.

Du kan använda alla unicode-tecken `,`i `\`etiketter utom `*`, och . Dessa tecken är reserverade. Om du vill inkludera ett reserverat `\{Reserved Character}`tecken måste du escape det med hjälp av .

### <a name="query-key-values"></a>Frågenyckelvärden

Varje nyckelvärde identifieras unikt med dess nyckel plus `null`en etikett som kan vara . Du frågar en App Configuration Store för nyckelvärden genom att ange ett mönster. Appkonfigurationsarkivet returnerar alla nyckelvärden som matchar mönstret och deras motsvarande värden och attribut. Använd följande nyckelmönster i REST API-anrop till appkonfiguration:

| Nyckel | |
|---|---|
| `key` utelämnas eller `key=*` | Matchar alla nycklar |
| `key=abc` | Matchar nyckelnamn **abc** exakt |
| `key=abc*` | Matchar nyckelnamn som börjar med **abc** |
| `key=abc,xyz` | Matchar viktiga namn **abc** eller **xyz**. Begränsad till fem csv:er |

Du kan också inkludera följande etikettmönster:

| Label (Etikett) | |
|---|---|
| `label` utelämnas eller `label=*` | Matchar alla etiketter, som innehåller`null` |
| `label=%00` | Matchningsetikett `null` |
| `label=1.0.0` | Matchar etiketten **1.0.0** exakt |
| `label=1.0.*` | Matchar etiketter som börjar med **1.0.** |
| `label=%00,1.0.0` | Matchar `null` etiketter eller **1.0.0**, begränsad till fem csv:er |

## <a name="values"></a>Värden

Värden som tilldelas till nycklar är också Unicode-strängar. Du kan använda alla Unicode-tecken för värden. Det finns en valfri användardefinierad innehållstyp som är associerad med varje värde. Använd det här attributet om du vill lagra information om ett värde som hjälper ditt program att bearbeta det korrekt.

Konfigurationsdata som lagras i ett App Configuration Store krypteras i vila och under överföring. Nycklarna är inte krypterade i vila. Appkonfiguration är inte en ersättningslösning för Azure Key Vault. Lagra inte programhemligheter i den.

## <a name="next-steps"></a>Nästa steg

* [Ögonblicksbild av punkt i tid](./concept-point-time-snapshot.md)  
* [Funktionshantering](./concept-feature-management.md)  
