---
title: Azure Service Fabric CLI - sfctl compose | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl compose kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 4b5cbb4a24b61de7e64a52ef950deedab3eec263
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667515"
---
# <a name="sfctl-compose"></a>sfctl compose
Skapa, ta bort och hantera Docker Compose-program.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar ett Service Fabric compose-distributionen. |
| lista | Hämtar listan över compose-distributioner som skapats i Service Fabric-klustret. |
| ta bort | Tar bort ett befintligt Service Fabric compose-distributionen från klustret. |
| status | Hämtar information om ett Service Fabric compose-distributionen. |
| uppgradering | Börjar uppgradera en Skriv-distribution i Service Fabric-klustret. |
| uppgraderingen-återställning | Återställa en compose-distributionen startar uppgraderingen i Service Fabric-klustret. |
| uppgraderingen-status | Hämtar information om den senaste uppgraderingen utförs på den här Service Fabric compose-distributionen. |

## <a name="sfctl-compose-create"></a>sfctl compose skapa
Skapar ett Service Fabric compose-distributionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributionsnamn [krävs] | Namnet på distributionen. |
| --filsökvägen [krävs] | Sökväg till målfilen Docker Compose. |
| --krypterad pass | I stället för att fråga om ett lösenord för behållarregister, använda ett redan krypterade lösenordet. |
| --has-pass | Efterfrågar lösenord till behållarregistret. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --användare | Användarnamn för anslutning till container registry. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-compose-list"></a>sfctl compose list
Hämtar listan över compose-distributioner som skapats i Service Fabric-klustret.

Hämtar status om compose-distributioner som har skapats eller håller på att skapas i Service Fabric-klustret. Svaret innehåller namn, status och annan information om Skriv-distributioner. Om listan över distributioner inte får plats på en sida, returneras en resultatsida samt ett fortsättningstoken som kan användas för att hämta nästa sida.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --continuation-token | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --max-results | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan mindre än de angivna maximalt antal resultat om de inte passar i meddelandet enligt storleksbegränsningar max meddelande definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlade frågan så många resultat som möjligt som passar in i svarsmeddelandet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-compose-remove"></a>sfctl compose remove
Tar bort ett befintligt Service Fabric compose-distributionen från klustret.

Tar bort ett befintligt Service Fabric compose-distributionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributionsnamn [krävs] | Identiteten för distributionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-compose-status"></a>sfctl compose status
Hämtar information om ett Service Fabric compose-distributionen.

Returnerar status för compose-distributionen som skapades eller håller på att skapas i Service Fabric-klustret och vars namn matchar det som anges som parameter. Svaret innehåller namn, status och annan information om hur du distribuerar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributionsnamn [krävs] | Identiteten för distributionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-compose-upgrade"></a>sfctl compose uppgradering
Börjar uppgradera en Skriv-distribution i Service Fabric-klustret.

Validerar angivna Uppgraderingsparametrar och börjar uppgradera distributionen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributionsnamn [krävs] | Namnet på distributionen. |
| --filsökvägen [krävs] | Sökvägen till målet Docker compose-fil. |
| --default-svc-type-health-map | JSON-kodad ordlista som beskriver hälsoprincip som används för att utvärdera hälsotillståndet för tjänster. |
| --krypterad pass | I stället för att fråga om ett lösenord för behållarregister, använda ett redan krypterade lösenordet. |
| --åtgärd vid uppgraderingsfel | Möjliga värden omfattar\: ”ogiltig”, ”återställa”, ”manuell”. |
| --force-restart | Processer kernelpaketet startas om under uppgraderingen även när code-versionen inte har ändrats. <br><br> Uppgraderingen ändras bara konfiguration eller data. |
| --has-pass | Efterfrågar lösenord till behållarregistret. |
| --health-check-retry | Hur lång tid mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfri. |
| --health-check-stable | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter du med nästa uppgraderingsdomän. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --health-check-wait | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du startar hälsotillståndet kontrollerar processen. |
| --replica-set-check | Längsta tid att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns ett oväntat problem. <br><br> När den här tidsgränsen upphör kommer att fortsätta bearbetningen av uppgraderingsdomänen oavsett förlust av tillgänglighetsproblem. Tidsgränsen återställs i början av varje uppgraderingsdomän. Giltiga värden är mellan 0 och 42949672925 inkluderande. |
| --svc-type-health-map | JSON-kodad lista över objekt som beskriver hälsoprinciper som används för att utvärdera hälsan för olika tjänsttyper. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --unhealthy-app | Högsta tillåtna procentandel program som är felaktiga innan den rapporterar ett fel. <br><br> Om du vill tillåta 10% av program vara felaktig, blir det här värdet 10. Procentandelen representerar högsta tolererat procentandelen av program som kan vara felaktig innan klustret anses av misstag. Om procentandelen respekteras men det finns minst en felaktigt program, utvärderas hälsotillståndet som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet instanser av programmet i klustret. |
| --upgrade-domain-timeout | Hur lång tid varje domän har slutförts innan FailureAction körs. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| – uppgradera typ | Standard\: löpande. |
| --upgrade-mode | Möjliga värden omfattar\: ”ogiltig”, ”UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Standard\: UnmonitoredAuto. |
| --upgrade-timeout | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --användare | Användarnamn för anslutning till container registry. |
| --warning-as-error | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compose uppgraderingen-återställning
Återställa en compose-distributionen startar uppgraderingen i Service Fabric-klustret.

Återställa en service fabric compose distributionsuppgradering.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributionsnamn [krävs] | Identiteten för distributionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compose uppgraderingen-status
Hämtar information om den senaste uppgraderingen utförs på den här Service Fabric compose-distributionen.

Returnerar information om tillståndet för Skriv distributionsuppgradering tillsammans med information för att underlätta felsökning hälsoproblem för programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --distributionsnamn [krävs] | Identiteten för distributionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).