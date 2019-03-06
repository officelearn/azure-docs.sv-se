---
title: Nyckelvärdeslagring för Azure App Configuration | Microsoft Docs
description: En översikt över hur konfigurationsdata lagras i Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f04067358b0b2bae745727a5dd7a1f5554f9f70e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884987"
---
# <a name="key-value-store"></a>Nyckelvärdeslagring

Azure App Configuration lagrar konfigurationsdata som nyckel/värde-par, ett enkelt men flexibelt sätt att representera olika typer av programinställningar som utvecklare är bekanta med.

## <a name="keys"></a>Nycklar

Nycklar fungerar som namn för nyckel/värde-par och används för att lagra och hämta motsvarande värden. Det är vanligt att organisera nycklar i en hierarkisk namnrymd med en teckenavgränsare (till exempel `/` eller `:`) baserat på en konvention som passar bäst för ditt program. App Configuration behandlar nycklar som en helhet. De parsar inte nycklar för att ta reda på hur deras namn är strukturerade eller för att framtvinga någon regel på dem.

Användningen av konfigurationsarkiv i programramverk kan kräva specifika namngivningskonventioner för nyckelvärden. Exempelvis definierar Spring Cloud-ramverket för Java `Environment`-resurser som ger inställningar till ett Spring-program till att parametriseras av variabler såsom *programnamn* och *profil*. Nycklar för Spring Clould-relaterade konfigurationsdata börjar vanligtvis med dessa två element separerade med en avgränsare.

Nycklar som lagras i App Configuration är skiftlägeskänsliga, Unicode-baserade strängar. Nycklarna *app1* och *App1* skiljer sig i ett appkonfigurationsarkiv. Ha detta i åtanke när du använder konfigurationsinställningar i ett program, eftersom vissa ramverk hanterar konfigurationsnycklar utan skiftlägeskänslighet. Till exempel behandlar konfigurationssystemet för ASP.NET Core nycklar som icke-skiftlägeskänsliga strängar. För att undvika oväntat beteende vid frågekörning mot App Configuration i ett ASP.NET Core-program bör nycklar som endast skiljs åt med skiftläge inte användas.

Du kan använda valfria Unicode-tecken i nyckelnamn som anges i App Configuration förutom `*`, `,` och `\`, som är reserverade. Om du behöver använda ett reserverat tecken måste du undanta det med hjälp av `\{Reserved Character}`. Det finns en sammanlagd storleksgräns på 10K tecken i ett nyckel/värde-par. Detta inkluderar alla tecken i nyckeln, dess värde samt alla tillhörande valfria attribut. Inom den här gränsen kan du ha många hierarkiska nivåer för nycklar.

### <a name="designing-key-namespaces"></a>Utforma nyckelnamnrymder

Det finns två generella metoder för namngivning av nycklar som används för konfigurationsdata: platt eller hierarkisk. De här metoderna är mycket lika vad gäller programanvändning, men den senare erbjuder flera fördelar:

* Enklare att läsa. I stället för en lång sekvens av tecken fungerar avgränsare i ett hierarkiskt nyckelnamn som blanksteg i en mening och ger naturlig radbrytningar mellan ord.
* Enklare att hantera. En nyckelnamnshierarki representerar logiska grupper med konfigurationsdata.
* Enklare att använda. Det är enklare att skriva en fråga som mönstermatchar nycklar i en hierarkisk struktur och bara hämtar en del av konfigurationsdata. Dessutom har många nyare programmeringsramverk inbyggt stöd för hierarkiska konfigurationsdata så att ditt program kan använda specifika konfigurationsuppsättningar.

Du kan organisera nycklar i App Configuration hierarkiskt på många sätt. Du kan betrakta sådana nycklar som [URI:er](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Varje hierarkisk nyckel är en *resurssökväg* som består av en eller flera komponenter som ansluts med avgränsare. Du kan välja vilket tecken som ska användas som avgränsare baserat på dina behov vad gäller program, programmeringsspråk eller ramverk. Du kan använda flera avgränsare för olika nycklar i App Configuration.

Här är flera exempel på hur du kan strukturera nyckelnamnen i en hierarki:

* Baserat på miljöer

        AppName:Test:DB:Endpoint
        AppName:Staging:DB:Endpoint
        AppName:Production:DB:Endpoint

* Baserat på komponenttjänster

        AppName:Service1:Test:DB:Endpoint
        AppName:Service1:Staging:DB:Endpoint
        AppName:Service1:Production:DB:Endpoint
        AppName:Service2:Test:DB:Endpoint
        AppName:Service2:Staging:DB:Endpoint
        AppName:Service2:Production:DB:Endpoint

* Baserat på distributionsregioner

        AppName:Production:Region1:DB:Endpoint
        AppName:Production:Region2:DB:Endpoint

### <a name="versioning-key-values"></a>Versionshantering för nyckelvärden

Som tillval kan nyckelvärden i App Configuration ha attributet **label** (etikett). Etiketter används för att skilja mellan nyckelvärden med samma nyckel. En nyckel som heter *app1* med etiketterna *v1* och *v2* bildar två separata nyckelvärden i ett appkonfigurationsarkiv. Som standard är etiketten för ett nyckelvärde tomt (eller `null`).

App Configuration versionshanterar inte nyckelvärden automatiskt när de ändras. Du kan använda etiketter som ett sätt att skapa flera versioner av ett nyckelvärde. Exempelvis kan du ange ett programversionsnummer eller ett Git-inchecknings-ID i etiketter för att identifiera nyckelvärden som är associerade med en viss programvaruversion.

Du kan använda valfria Unicode-tecken i etiketter förutom `*`, `,` och `\`, som är reserverade. Om du behöver använda ett reserverat tecken måste du undanta det med hjälp av `\{Reserved Character}`.

### <a name="querying-key-values"></a>Frågekörning för nyckelvärden

Varje nyckelvärde identifieras unikt genom sin nyckel plus en etikett som kan vara `null`. Du kan köra frågor mot ett appkonfigurationsarkiv för nyckelvärden genom att ange ett mönster. Appkonfigurationsarkivet returnerar alla nyckelvärden som matchar mönstret och deras motsvarande värden och attribut. Du kan använda följande nyckelmönster i REST API-anrop till App Configuration:

| Nyckel | |
|---|---|
| `key` utelämnas eller `key=*` | Matchar alla nycklar |
| `key=abc` | Matchar nyckelnamnet **abc** exakt. |
| `key=abc*` | Matchar nyckelnamn som börjar med **abc** |
| `key=*abc` | Matchar nyckelnamn som slutar med **abc** |
| `key=*abc*` | Matchar nyckelnamn som innehåller **abc** |
| `key=abc,xyz` | Matchar nyckelnamnet **abc** eller **xyz**, begränsat till 5 CSV:er |

Du kan även inkludera följande etikettmönster:

| Label (Etikett) | |
|---|---|
| `label` utelämnas eller `label=*` | Matchar valfri etikett, inklusive `null` |
| `label=%00` | Matchningar etiketten `null`. |
| `label=1.0.0` | Matchar etiketten **1.0.0** exakt |
| `label=1.0.*` | Matchar etiketter som börjar med **1.0.** |
| `label=*.0.0` | Matchar etiketter som slutar med **.0.0** |
| `label=*.0.*` | Matchar etiketter som innehåller **.0.** |
| `label=%00,1.0.0` | Matchar etiketten `null` eller **1.0.1**, begränsat till 5 CSV:er |

## <a name="values"></a>Värden

Värden som tilldelas till nycklar är också Unicode-strängar. Du kan använda alla Unicode-tecken för värden. Det finns en valfri användardefinierad **innehållstyp** som är associerad med varje värde. Du kan använda det här attributet för att lagra information (till exempel kodningsschema) om ett värde som hjälper programmet att behandla det korrekt.

Konfigurationsdata som lagras i ett appkonfigurationsarkiv, däribland alla nycklar och värden, krypteras i vila och under överföring. App Configuration är dock inte en ersättningslösning för Azure Key Vault. Du bör inte lagra programhemligheter i den.

## <a name="next-steps"></a>Nästa steg

* [Begrepp: Ögonblicksbild av lagring vid olika tidpunkter](concept-point-time-snapshot.md)  
