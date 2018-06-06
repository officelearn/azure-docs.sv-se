---
title: Azure Service Fabric CLI - sfctl utgöra | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl utgöra kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cc3d3e35ce3dd457d981dfe9420be765cf9fc45a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763416"
---
# <a name="sfctl-compose"></a>sfctl compose
Skapa, ta bort och hantera Docker Compose program.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar ett Service Fabric skapa distributionen. |
| lista | Hämtar listan över utgöra distributioner som skapats i Service Fabric-klustret. |
| ta bort | Tar bort en befintlig Service Fabric skapa distribution från klustret. |
| status | Hämtar information om ett Service Fabric skapa distributionen. |
| Uppgradera | Börja uppgradera en Skriv-distribution i Service Fabric-klustret. |
| uppgradera status | Hämtar information om den senaste uppgraderingen utförs på den här Service Fabric skapa distributionen. |

## <a name="sfctl-compose-create"></a>Skapa sfctl skapa
Skapar ett Service Fabric skapa distributionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --distributionsnamnet [krävs] | Namnet på distributionen. |
| --filsökvägen [krävs] | Sökväg till målfilen Docker Compose. |
| --krypterade pass | I stället för att fråga efter behållaren registret lösenord, använda ett redan krypterade lösenordet. |
| --har pass | Ombeds du att ett lösenord i registret för behållaren. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --användare | Användarnamn för att ansluta till registret för behållaren. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-compose-list"></a>sfctl utgöra lista
Hämtar listan över utgöra distributioner som skapats i Service Fabric-klustret.

Hämtar status om Skriv-distributioner som har skapats eller håller på att skapas i Service Fabric-klustret. Svaret innehåller namn, status och annan information om Skriv-distributioner. Om listan över distributioner inte får plats på en sida, returneras en sida av resultaten samt en fortsättningstoken som kan användas för att hämta nästa sida.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --max-resultat | Högsta antal resultat ska returneras som en del av växlingsbar frågor. Den här parametern anger den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan vara mindre än de angivna maximala resultat om de inte passar i meddelandet enligt max meddelandet storlek restriktioner definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlingsbara frågan så många resultat som möjligt som ryms i svarsmeddelandet. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-compose-remove"></a>sfctl utgöra ta bort
Tar bort en befintlig Service Fabric skapa distribution från klustret.

Tar bort en befintlig Service Fabric skapa distributionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --distributionsnamnet [krävs] | Identiteten för distributionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-compose-status"></a>sfctl utgöra status
Hämtar information om ett Service Fabric skapa distributionen.

Returnerar status för distributionen Skriv som har skapats eller håller på att skapas i Service Fabric-kluster och vars namn matchar den som anges som parameter. Svaret innehåller namn, status och annan information om distributionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --distributionsnamnet [krävs] | Identiteten för distributionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-compose-upgrade"></a>sfctl utgöra uppgradering
Börja uppgradera en Skriv-distribution i Service Fabric-klustret.

Validerar angivna uppgradera parametrarna och börja uppgradera distributionen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --distributionsnamnet [krävs] | Namnet på distributionen. |
| --filsökvägen [krävs] | Sökvägen till målet Docker compose fil. |
| – standard-svc-typ-hälsa-karta | JSON-kodade ordlista som beskriver hälsoprincipen används för att utvärdera hälsan för tjänster. |
| --krypterade pass | I stället för att fråga efter behållaren registret lösenord, använda ett redan krypterade lösenordet. |
| --åtgärd vid | Möjliga värden är\: 'Ogiltig ”,” återställa ”,” manuell ”. |
| --force-restart | Tvinga fram omstart. |
| --har pass | Ombeds du att ett lösenord i registret för behållaren. |
| --hälsa-check-återförsök | Health check försök timeout mätt i millisekunder. |
| --health check stabilt | Hälsokontroll stabil tid, mätt i millisekunder. |
| --hälsokontroll- | Health check vänta varaktighet mätt i millisekunder. |
| --replik-set-kontroll | Uppgradera replikuppsättningen Kontrollera timeout mätt i sekunder. |
| --svc-typ-hälsa-karta | JSON-kodad lista över objekt som beskriver hälsoprinciper som används för att utvärdera hälsan för olika typer. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --ohälsosamt-app | Maximalt tillåten procentandel av felaktiga program innan ett fel rapporteras. <br><br> Om du vill tillåta 10% av program feltillstånd exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av program som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst ett feltillstånd program, utvärderas hälsa som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret. |
| --uppgraderingen domäntidsgräns | Uppgraderingsdomänen mätt i millisekunder. |
| --typ av uppgradering | Som standard\: rullande. |
| --uppgraderingen-läge | Möjliga värden är\: 'Ogiltig', 'UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Som standard\: UnmonitoredAuto. |
| --uppgraderingen-timeout | Tidsgränsen för uppgradering mätt i millisekunder. |
| --användare | Användarnamn för att ansluta till registret för behållaren. |
| --warning-as-error | Varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl utgöra uppgraderingen-status
Hämtar information om den senaste uppgraderingen utförs på den här Service Fabric skapa distributionen.

Returnerar information om tillståndet för Skriv distribution uppgraderingen tillsammans med information för att underlätta felsökning programproblem hälsa.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --distributionsnamnet [krävs] | Identiteten för distributionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |



## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).