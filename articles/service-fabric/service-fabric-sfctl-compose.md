---
title: Azure Service Fabric CLI- sfctl komponera
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för Docker Compose-program.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906126"
---
# <a name="sfctl-compose"></a>sfctl compose
Skapa, ta bort och hantera Docker Compose-program.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar en service fabric-sammansättningsdistribution. |
| lista | Hämtar listan över sammansättningsdistributioner som skapats i Service Fabric-klustret. |
| Ta bort | Tar bort en befintlig Service Fabric-sammansättningsdistribution från klustret. |
| status | Hämtar information om en distribution av Service Fabric-sammansättning. |
| uppgradera | Startar uppgradering av en sammansättningsdistribution i Service Fabric-klustret. |
| återställning av uppgradering | Startar att återställa en uppgradering av sammansättningsdistributionen i Service Fabric-klustret. |
| uppgraderingsstatus | Hämtar information om den senaste uppgraderingen som utförts på den här distributionen av Service Fabric-sammansättning. |

## <a name="sfctl-compose-create"></a>sfctl komponera skapa
Skapar en service fabric-sammansättningsdistribution.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --deployment-name [Obligatoriskt] | Namnet på distributionen. |
| --file-path [Obligatoriskt] | Sökväg till måldockerkompostfilen. |
| --krypterat-pass | I stället för att fråga efter ett lösenord för behållarregister använder du en redan krypterad lösenfras. |
| --har-passera | Kommer att fråga efter ett lösenord till behållarregistret. |
| --timeout -t | Standard\: 60. |
| --användare | Användarnamn för att ansluta till behållarregistret. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-compose-list"></a>sfctl komponera lista
Hämtar listan över sammansättningsdistributioner som skapats i Service Fabric-klustret.

Hämtar status om de sammansättningsdistributioner som har skapats eller håller på att skapas i service fabric-klustret. Svaret innehåller namn, status och annan information om sammansättningsdistributionerna. Om listan över distributioner inte får plats på en sida returneras en resultatsida samt en fortsättningstoken, som kan användas för att hämta nästa sida.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --max-resultat | Det maximala antalet resultat som ska returneras som en del av de växlingsdelade frågorna. Den här parametern definierar den övre gränsen för antalet returnerade resultat. De returnerade resultaten kan vara mindre än de angivna maximala resultaten om de inte får plats i meddelandet enligt de begränsningar för högsta meddelandestorlek som definierats i konfigurationen. Om den här parametern är noll eller inte angiven innehåller den växlingsbara frågan så många resultat som möjligt som passar i returmeddelandet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-compose-remove"></a>sfctl komponera ta bort
Tar bort en befintlig Service Fabric-sammansättningsdistribution från klustret.

Tar bort en befintlig tjänst fabric-sammansättningsdistribution.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --deployment-name [Obligatoriskt] | Identiteten på distributionen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-compose-status"></a>sfctl komponera status
Hämtar information om en distribution av Service Fabric-sammansättning.

Returnerar status för den sammansättningsdistribution som skapades eller håller på att skapas i service fabric-klustret och vars namn matchar den som anges som parameter. Svaret innehåller namn, status och annan information om distributionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --deployment-name [Obligatoriskt] | Identiteten på distributionen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-compose-upgrade"></a>sfctl komponera uppgradering
Startar uppgradering av en sammansättningsdistribution i Service Fabric-klustret.

Validerar de medföljande uppgraderingsparametrarna och börjar uppgradera distributionen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --deployment-name [Obligatoriskt] | Namnet på distributionen. |
| --file-path [Obligatoriskt] | Sökväg till måldockerns skrivfil. |
| --default-svc-type-health-map --default-svc-type-health-map --default-svc-type-health-map -- | JSON kodade ordlista som beskriver hälsoprincipen som används för att utvärdera hälsotillståndet för tjänster. |
| --krypterat-pass | I stället för att fråga efter ett lösenord för behållarregister använder du en redan krypterad lösenfras. |
| --fel-åtgärd | Möjliga värden\: är "Ogiltig", "Återställning", "Manuell". |
| --force-restart | Processer startas om kraftfullt under uppgraderingen även när kodversionen inte har ändrats. <br><br> Uppgraderingen ändrar bara konfiguration eller data. |
| --har-passera | Kommer att fråga efter ett lösenord till behållarregistret. |
| --hälsokontroll-nyttförsök | Hur lång tid det går mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfritt. |
| --hälsokontroll-stabil | Den tid som programmet eller klustret måste förbli felfritt innan uppgraderingen fortsätter till nästa uppgraderingsdomän. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälsa-check-vänta | Hur lång tid du ska vänta efter att ha slutfört en uppgraderingsdomän innan hälsokontrollprocessen startas. |
| --replica-set-check | Den maximala tiden för att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns oväntade problem. <br><br> När den här timeouten går ut fortsätter bearbetningen av uppgraderingsdomänen oavsett problem med tillgänglighetsförlust. Tidsgränsen återställs i början av varje uppgraderingsdomän. Giltiga värden är mellan 0 och 42949672925. |
| --svc-typ-hälsa-karta | JSON kodade lista över objekt som beskriver hälsoprinciper som används för att utvärdera hälsotillståndet för olika tjänsttyper. |
| --timeout -t | Standard\: 60. |
| --ohälsosam-app | Den högsta tillåtna procentandelen felaktiga program innan du rapporterar ett fel. <br><br> Om du till exempel vill att 10 % av programmen ska vara felaktiga, skulle det här värdet vara 10. Procentsatsen representerar den maximala tolererade procentandelen av program som kan vara felaktiga innan klustret betraktas som ett fel. Om procentsatsen respekteras men det finns minst ett felaktigt program utvärderas hälsotillståndet som Varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret. |
| --upgrade-domain-timeout --upgrade-domain-timeout --upgrade-domain-timeout -- | Den tid som varje uppgraderingsdomän måste slutföra innan FailureAction körs. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --upgrade-kind | Standardrullning.\: |
| --upgrade-mode | Möjliga värden\: är "Ogiltig", "UnmonitoredAuto", "UnmonitoredManual", "Övervakad".  Standard\: unmonitoredauto. |
| --upgrade-timeout --upgrade-timeout --upgrade-timeout -- | Hur lång tid den totala uppgraderingen måste slutföras innan FailureAction körs. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --användare | Användarnamn för att ansluta till behållarregistret. |
| --varning-som-fel | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl komponera uppgradering-rollback
Startar att återställa en uppgradering av sammansättningsdistributionen i Service Fabric-klustret.

Återställning av en tjänstinfrastruktur komponerar distributionsuppgradering.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --deployment-name [Obligatoriskt] | Identiteten på distributionen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl komponera uppgradering-status
Hämtar information om den senaste uppgraderingen som utförts på den här distributionen av Service Fabric-sammansättning.

Returnerar informationen om tillståndet för distributionsuppgraderingen för komponerad distribution tillsammans med information för att underlätta felsökning av programhälsoproblem.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --deployment-name [Obligatoriskt] | Identiteten på distributionen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).