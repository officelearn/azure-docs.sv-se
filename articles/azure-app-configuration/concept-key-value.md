---
title: Förstå Azure App konfigurations nyckel-värde lager
description: Förstå hur konfigurations data lagras i Azure App-konfigurationen.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523612"
---
# <a name="keys-and-values"></a>Nycklar och värden

Azure App konfiguration lagrar konfigurations data som nyckel/värde-par. Nyckel/värde-par är en enkel och flexibel representation av program inställningar som används av utvecklare.

## <a name="keys"></a>Nycklar

Nycklar fungerar som identifierare för nyckel/värde-par och används för att lagra och hämta motsvarande värden. Det är en vanlig metod för att ordna nycklar i ett hierarkiskt namn område med hjälp av en tecken avgränsare, till exempel `/` eller `:`. Använd en konvention som passar bäst för ditt program. App Configuration behandlar nycklar som en helhet. Det går inte att parsa nycklar för att ta reda på hur deras namn är strukturerade eller tillämpa någon regel på dem.

Här följer två exempel på nyckel namn som är strukturerade i en hierarki:

* Baserat på komponenttjänster

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Baserat på distributionsregioner

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

Användningen av konfigurations data i Application Framework kan diktera specifika namngivnings scheman för nyckel värden. Till exempel definierar `Environment` Javas moln ramverk resurser som tillhandahåller inställningar för ett våren-program.  Dessa parametrar är parameterstyrda av variabler som innehåller *program namn* och *profil*. Nycklar för vår moln-relaterade konfigurations data börjar normalt med dessa två element, avgränsade med en avgränsare.

Nycklar som lagras i App Configuration är skiftlägeskänsliga, Unicode-baserade strängar. Nycklarna *APP1* och *APP1* är distinkta i ett konfigurations Arkiv för appar. Tänk på detta när du använder konfigurations inställningar i ett program eftersom vissa ramverk hanterar konfigurations nycklar SKIFT läges okänsligt. Vi rekommenderar inte att du använder SKIFT läge för att särskilja nycklar.

Du kan använda valfritt Unicode `*`-tecken i nyckel namn förutom, `,`, och `\`.  Om du behöver inkludera ett av dessa reserverade tecken kan du kringgå det genom att `\{Reserved Character}`använda. 

Det finns en sammanlagd storleks gräns på 10 KB på ett nyckel/värde-par. Den här gränsen omfattar alla tecken i nyckeln, dess värde och alla tillhör ande valfria attribut. Inom den här gränsen kan du ha många hierarkiska nivåer för nycklar.

### <a name="design-key-namespaces"></a>Design nyckel namn rymder

Det finns två generella metoder för namngivning av nycklar som används för konfigurationsdata: platt eller hierarkisk. Dessa metoder liknar ett program användnings synpunkt, men hierarkiska namn erbjuder ett antal fördelar:

* Enklare att läsa. Avgränsare i ett hierarkiskt nyckel namn fungerar som blank steg i en mening. De ger också naturliga brytningar mellan ord.
* Enklare att hantera. En nyckelnamnshierarki representerar logiska grupper med konfigurationsdata.
* Enklare att använda. Det är enklare att skriva en fråga som mönstermatchar nycklar i en hierarkisk struktur och bara hämtar en del av konfigurationsdata. Dessutom har många nyare programmerings ramverk inbyggda stöd för hierarkiska konfigurations data, så att ditt program kan använda specifika uppsättningar konfiguration.

Du kan organisera nycklar i App Configuration hierarkiskt på många sätt. Tänk på sådana nycklar som [URI: er](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Varje hierarkisk nyckel är en resurs *Sök väg* bestående av en eller flera komponenter som kopplas samman med avgränsare. Välj vilket tecken som ska användas som avgränsare baserat på vad programmet, programmeringsspråket eller ramverket behöver. Använd flera avgränsare för olika nycklar i app-konfigurationen.

### <a name="label-keys"></a>Etikett nycklar

Nyckel värden i app-konfigurationen kan också ha ett etikett-attribut. Etiketter används för att särskilja nyckel värden med samma nyckel. En viktig *APP1* med etiketter *A* och *B* formar två separata nycklar i ett konfigurations lager för appar. Som standard har ett nyckel värde ingen etikett. Om du explicit vill referera till ett nyckel värde utan en `\0` etikett använder du ( `%00`URL kodad som).

Label är ett bekvämt sätt att skapa varianter av en nyckel. En vanlig användning av etiketter är att ange flera miljöer för samma nyckel:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Versions nyckel värden

App-konfigurationen har inte automatiskt versions nyckel värden. Använd etiketter som ett sätt att skapa flera versioner av ett nyckel värde. Du kan till exempel ange ett program versions nummer eller ett git-bekräftelse-ID i etiketter för att identifiera nyckel värden som associeras med en viss program varu version.

Du kan använda valfritt Unicode `*`-tecken i etiketter förutom, `,`, och `\`. Dessa tecken är reserverade. Om du vill inkludera ett reserverat tecken måste du kringgå `\{Reserved Character}`det med.

### <a name="query-key-values"></a>Fråga nyckel värden

Varje nyckel värde identifieras unikt med nyckeln plus en etikett som kan vara `null`. Du frågar en app Configuration Store efter nyckel värden genom att ange ett mönster. Konfigurations arkivet för appar returnerar alla nyckel värden som matchar mönstret och deras motsvarande värden och attribut. Använd följande nyckel mönster i REST API anrop till app-konfiguration:

| Nyckel | |
|---|---|
| `key` utelämnas eller `key=*` | Matchar alla nycklar |
| `key=abc` | Matchar nyckel namn **ABC** exakt |
| `key=abc*` | Matchar nyckelnamn som börjar med **abc** |
| `key=abc,xyz` | Matchar nyckel namn **ABC** eller **XYZ**. Begränsad till fem CSV: er |

Du kan också inkludera följande etikett mönster:

| Label (Etikett) | |
|---|---|
| `label` utelämnas eller `label=*` | Matchar alla etiketter, som innehåller`null` |
| `label=%00` | Matchar `null` etikett |
| `label=1.0.0` | Matchar etiketten **1.0.0** exakt |
| `label=1.0.*` | Matchar etiketter som börjar med **1.0.** |
| `label=%00,1.0.0` | Matchar etiketter `null` eller **1.0.0**, begränsade till fem CSV: er |

## <a name="values"></a>Värden

Värden som tilldelas till nycklar är också Unicode-strängar. Du kan använda alla Unicode-tecken för värden. Det finns en valfri användardefinierad innehållstyp som är associerad med varje värde. Använd det här attributet för att lagra information om ett värde som hjälper ditt program att bearbeta det korrekt.

Konfigurations data som lagras i ett konfigurations Arkiv för appar krypteras i vila och under överföring. Nycklarna är inte krypterade i vila. App-konfigurationen är inte en ersättnings lösning för Azure Key Vault. Lagra inte program hemligheter i det.

## <a name="next-steps"></a>Nästa steg

* [Tidpunktsbaserad ögonblicksbild](./concept-point-time-snapshot.md)  
* [Funktionshantering](./concept-feature-management.md)  
