---
title: Azure Service Fabric CLI - sfctl program | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl program-kommandon.
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
ms.openlocfilehash: 3ecc5a03ff1847dc11c5a5047e35566a4e68fec2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763705"
---
# <a name="sfctl-application"></a>sfctl application
Skapa, ta bort och hantera program och programtyper.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar ett Service Fabric-program med den angivna beskrivningen. |
| radera | Tar bort ett befintligt Service Fabric-program. |
| Distribueras | Hämtar information om ett program distribueras på ett Service Fabric-nod. |
| distribuerat hälsa | Hämtar information om hälsotillståndet för ett program som distribuerats på en Service Fabric-nod. |
| distribuerat lista | Hämtar listan över program som distribueras på ett Service Fabric-nod. |
| hälsotillstånd | Hämtar hälsotillståndet för service fabric-programmet. |
| info | Hämtar information om ett Service Fabric-program. |
| lista | Hämtar listan över program som skapats i Service Fabric-klustret som matchar de angivna filtren. |
| läsa in | Hämtar att läsa in information om ett Service Fabric-program. |
| Manifestet | Hämtar manifestet som beskriver en typ av program. |
| Etablera | Tillhandahåller eller registrerar skriver ett Service Fabric-program med klustret med .sfpkg paketet i externa store eller använda programpaketet i image store. |
| rapporten hälsa | Skickar en hälsorapport för Service Fabric-programmet. |
| typ | Hämtar listan över programtyper i Service Fabric-kluster som matchar exakt det angivna namnet. |
| Ange-lista | Hämtar listan över programtyper i Service Fabric-klustret. |
| Avetablera | Tar bort eller Avregistrerar en Service Fabric programtyp från klustret. |
| Uppgradera | Börja uppgradera ett program i Service Fabric-klustret. |
| återuppta uppgradering | Återupptar uppgradera ett program i Service Fabric-klustret. |
| uppgraderingen återställning | Startar återställer för närvarande löpande uppgradering av ett program i Service Fabric-klustret. |
| uppgradera status | Hämtar information för den senaste uppgraderingen utförs på det här programmet. |
| upload | Kopiera ett Service Fabric-programpaket till image store. |

## <a name="sfctl-application-create"></a>Skapa sfctl program
Skapar ett Service Fabric-program med den angivna beskrivningen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – appens namn-[krävs] | Namnet på programmet, inklusive den ' fabric\:-URI-schema. |
| appen--typ [krävs] | Typnamn program finns i programmanifestet. |
| --app-versionen [krävs] | Versionen av programtyp som definierats i programmanifestet. |
| --nod maxantal | Maximalt antal noder där Service Fabric kommer reserverad kapacitet för det här programmet. Observera att detta inte innebär att tjänster i det här programmet kommer att placeras på alla dessa noder. |
| --mått | En JSON-kodade lista över program kapacitet mått beskrivningar. Ett mått har definierats som ett namn som är associerad med en uppsättning kapaciteter för varje nod som programmet finns på. |
| --min nodsantalet | Det minsta antalet noder där Service Fabric kommer reserverad kapacitet för det här programmet. Observera att detta inte innebär att tjänster i det här programmet kommer att placeras på alla dessa noder. |
| --parametrar | En JSON-kodade lista över program parametern åsidosättningar för att användas när du skapar programmet. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-delete"></a>ta bort sfctl-program
Tar bort ett befintligt Service Fabric-program.

Tar bort ett befintligt Service Fabric-program. Ett program måste skapas innan den kan tas bort. Om du tar bort ett program, ta bort alla tjänster som ingår i programmet. Som standard görs Service Fabric försök att stänga service repliker på ett korrekt sätt och ta bort tjänsten. Men om en tjänst har problem med att stänga repliken, delete-åtgärden kan ta lång tid eller fastnar. Använda flaggan valfria ForceRemove hoppa över korrekt sekvensstängning och ta bort programmet och alla dess tjänster tvång.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --force-ta bort | Ta bort ett Service Fabric-program eller tjänst tvång utan att gå igenom alla korrekt avslutning. Den här parametern kan användas för att tvång ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt Stäng av replikerna. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-deployed"></a>sfctl program distribueras
Hämtar information om ett program distribueras på ett Service Fabric-nod.

Hämtar information om ett program distribueras på ett Service Fabric-nod.  Den här frågan returnerar information om programmet om det program-ID som angetts för system-program. Resultaten omfattar distribuerade program i aktiva, aktivera och hämtar tillstånd. Den här frågan kräver att nodnamnet motsvarar en nod i klustret. Frågan misslyckas om det angivna nodnamnet inte pekar på någon aktiv Service Fabric-noder i klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| --inkluderar hälsotillstånd | Inkludera hälsotillståndet för en entitet. Om den här parametern är false eller har inte angetts, är hälsotillståndet returnerade ”okänt”. När värdet är true frågan sträcker sig parallellt med noden och tjänsten för hälsotillstånd system innan resultaten slås samman. Därför är dyrare frågan och kan ta längre tid. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-deployed-health"></a>sfctl program distribueras hälsa
Hämtar information om hälsotillståndet för ett program som distribuerats på en Service Fabric-nod.

Hämtar information om hälsotillståndet för ett program som distribuerats på en Service Fabric-nod. Använd EventsHealthStateFilter att du kan också filtrera för insamling av HealthEvent objekt som rapporterats för det distribuerade programmet baseras på hälsotillståndet. Använd DeployedServicePackagesHealthStateFilter att du kan också filtrera för DeployedServicePackageHealth underordnade baseras på hälsotillståndet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| --DEPLOYED-Service-Packages-Health-State-filter | Tillåter filtrering av distribuerade tjänsten paketet hälsa tillstånd objekten som returneras i resultatet av distribuerade programmets hälsotillstånd fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast distribuera tjänsten paket som matchar filtret returneras. Alla distribuerade tjänstpaket används för att utvärdera aggregerade hälsotillståndet för det distribuerade programmet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för servicepaket med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --Utelämna hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-deployed-list"></a>sfctl distribueras-lista
Hämtar listan över program som distribueras på ett Service Fabric-nod.

Hämtar listan över program som distribueras på ett Service Fabric-nod. Resultatet innehåller ingen information om distribuerade systemprogram om du inte uttryckligen frågas efter-ID. Resultaten omfattar distribuerade program i aktiva, aktivera och hämtar tillstånd. Den här frågan kräver att nodnamnet motsvarar en nod i klustret. Frågan misslyckas om det angivna nodnamnet inte pekar på någon aktiv Service Fabric-noder i klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --inkluderar hälsotillstånd | Inkludera hälsotillståndet för en entitet. Om den här parametern är false eller har inte angetts, är hälsotillståndet returnerade ”okänt”. När värdet är true frågan sträcker sig parallellt med noden och tjänsten för hälsotillstånd system innan resultaten slås samman. Därför är dyrare frågan och kan ta längre tid. |
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

## <a name="sfctl-application-health"></a>sfctl programs hälsa
Hämtar hälsotillståndet för service fabric-programmet.

Returnerar hed tillståndet för service fabric-programmet. Svaret rapporterar hälsotillståndet Ok, fel eller varning. Om enheten inte hittas i health store, returneras fel.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --deployed-applications-health-state-filter | Tillåter filtrering av distribuerade program hälsa tillstånd objekt returneras i resultatet av programmets hälsotillstånd fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast distribuerade program som matchar filtret kommer att returneras. Alla distribuerade program används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för distribuerade program med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --Utelämna hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --services-health-state-filter | Tillåter filtrering av tjänster hälsa tillstånd objekt returneras i resultatet av tjänster hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast tjänster som matchar filtret returneras. Alla tjänster som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 ska sedan hälsotillståndet för tjänster med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-info"></a>sfctl programinformation
Hämtar information om ett Service Fabric-program.

Returnerar information om det program som har skapats eller håller på att skapas i Service Fabric-kluster och vars namn matchar den som anges som parameter. Svaret innehåller namn, typ, status, parametrar och annan information om programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Utelämna applikationsparametrarna | Flagga som anger om programmet parametrar kommer att uteslutas från resultatet. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-list"></a>sfctl lista
Hämtar listan över program som skapats i Service Fabric-klustret som matchar de angivna filtren.

Hämtar information om program som har skapats eller håller på att skapas i Service Fabric-kluster och som matchar de angivna filtren. Svaret innehåller namn, typ, status, parametrar och annan information om programmet. Om programmen inte får plats på en sida, returneras en sida av resultaten samt en fortsättningstoken som kan användas för att hämta nästa sida. Filter ApplicationTypeName och ApplicationDefinitionKindFilter kan inte anges samtidigt.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --application-definition-kind-filter | Används för att filtrera på ApplicationDefinitionKind, vilket är den mekanism som används för att definiera ett Service Fabric-program.  <br> -Standard - standardvärde som utför samma funktion som du väljer ”alla”. Värdet är 0.  <br> -Alla - Filter som matchar indata med ett ApplicationDefinitionKind-värde. Värdet är 65535.  <br> -ServiceFabricApplicationDescription - Filter som matchar indata med ApplicationDefinitionKind värdet ServiceFabricApplicationDescription. Värdet är 1.  <br> -Skriv - Filter som matchar indata med ApplicationDefinitionKind värdet Compose. Värdet är 2. |
| --programmets typnamn | Programnamnet för typ som används för att filtrera de program som ska fråga efter. Det här värdet får inte innehålla typen programversionen. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --Utelämna applikationsparametrarna | Flagga som anger om programmet parametrar kommer att uteslutas från resultatet. |
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

## <a name="sfctl-application-load"></a>sfctl programinläsning
Hämtar att läsa in information om ett Service Fabric-program.

Returnerar läsa in information om programmet som har skapats eller håller på att skapas i Service Fabric-kluster och vars namn matchar den som anges som parameter. Svaret innehåller namnet, minsta noder, maximalt antal noder, antalet noder som programmet för närvarande använder, och programmet belastningen mått information om programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-manifest"></a>sfctl programmanifestet
Hämtar manifestet som beskriver en typ av program.

Hämtar manifestet som beskriver en typ av program. Svaret innehåller programmanifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs] | Namnet på programtyp. |
| --program-type-version [krävs] | Versionen av programtypen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-provision"></a>sfctl programmet etablera
Tillhandahåller eller registrerar skriver ett Service Fabric-program med klustret med .sfpkg paketet i externa store eller använda programpaketet i image store.

Etablerar ett Service Fabric programtyp med klustret. Detta krävs innan några nya program kan initieras. Etableringsåtgärden kan utföras antingen på programpaket som anges av relativePathInImageStore eller med hjälp av URI för externa .sfpkg. Om inte--externa etablera anges förväntar det här kommandot image store etablera.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-paketet-hämtning-uri | Sökvägen till '.sfpkg' programpaket varifrån programpaketet kan hämtas med hjälp av HTTP eller HTTPS-protokoll. <br><br> Lagra endast för etablera typ externa. Programpaketet kan lagras i en extern butik som innehåller GET-åtgärden för att hämta filen. Protokoll som stöds är HTTP och HTTPS och sökvägen måste tillåta läsåtkomst. |
| ---typ-build-programsökväg | För att etablera kind avbildningsarkivet endast. Den relativa sökvägen för programpaket i image store anges under föregående överföringen. |
| --programmets typnamn | Lagra endast för etablera typ externa. Namnet på programmet står namnet på programtyp som hittades i programmanifestet. |
| --typ programversion | Lagra endast för etablera typ externa. Programmets Typversion representerar versionen av programtyp som hittades i programmanifestet. |
| --externa etablera | Platsen där programpaket kan registreras eller etableras. Anger att tillhandahållande av ett programpaket som tidigare har överförts till en extern butik. Programpaketet slutar med tillägget *.sfpkg. |
| --Nej vänta | Anger huruvida etablering ska ske asynkront. <br><br> Om värdet är true, etableringsåtgärd returnerar fortsätter när begäran har godkänts av systemet och etableringsåtgärden utan timeout-gränsen. Standardvärdet är false. För stora programpaket rekommenderar vi att ange värdet till true. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-report-health"></a>sfctl rapport-programhälsan
Skickar en hälsorapport för Service Fabric-programmet.

Rapporterar hälsotillståndet för det angivna Service Fabric-programmet. Rapporten innehålla information om källan för hälsorapport och egenskapen som har rapporterats. Rapporten skickas till Service Fabric-gateway som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen men avvisas av health store efter extra validering. Health store kan avvisa rapporten på grund av en ogiltig parameter som ett inaktuella sekvensnummer. Hämta programmets hälsotillstånd för att se om rapporten har tillämpats i health store, och kontrollera att rapporten visas.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. <br><br> Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den '\~' tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ' myapp\~app1' i 6.0 + och ' myapp/app1 ”i tidigare versioner. |
| --hälsa egenskapen [krävs] | Egenskapen för information om hälsa. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport möjlighet att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan övervaka tillståndet för ledigt på en nod, så att den kan rapportera ”AvailableDisk”-egenskapen på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. I health store behandlas rapporterna som separata hälsa händelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen fram hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden är\: 'Ogiltig', 'Ok', 'Varning', 'Fel', 'Okänt'. |
| --käll-id [krävs] | Källnamn som identifierar komponenten klient-watchdog-systemet som genererats fram hälsoinformation. |
| – Beskrivning | Beskrivning av informationen om hälsa. <br><br> Den representerar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Den angivna strängen är längre, trunkeras den automatiskt. När trunkerad, de sista tecknen i beskrivningen innehåller en markör ”[Truncated]” och totala strängens storlek är 4096 tecken. Förekomst av markören som anger att användare som trunkering inträffade. Observera att när trunkerad, beskrivningen har mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till Service Fabric-gateway som vidarebefordrar till health store. Om Immediate anges till true, rapporten skickas direkt från http-Gateway i health store, oavsett fabric-klientinställningarna som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas rapporten skickas fortfarande, till exempel om HTTP-Gateway är stängt eller meddelandet inte nå gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningar hälsa från HTTP-Gateway. Det är därför batchar enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom det tillåter hälsa klienten att optimera hälsotillståndsrapportering meddelanden till health store, samt för bearbetning. Som standard skickas rapporter inte omedelbart. |
| – ta bort när-har upphört | Värde som anger om rapporten tas bort från health store när den upphör. <br><br> Om värdet är true, rapporten har tagits bort från health store när den upphör. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). Det här sättet är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnummer rapporten används av health store för att identifiera inaktuella rapporter. Om inget anges genereras ett sekvensnummer automatiskt av klientens hälsa när en rapport har lagts till. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --TTL-värde | Den tid som den här hälsorapport är giltigt. Det här fältet formatet ISO8601 för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, skickar de rapporter med högre frekvens än livslängd. De kan ange time to live till oändliga om klienter rapport i övergång. När time to live-upphör att gälla hälsohändelse som innehåller information om hälsa är antingen bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten på live oändligt värde som standard. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-type"></a>sfctl programtyp
Hämtar listan över programtyper i Service Fabric-kluster som matchar exakt det angivna namnet.

Returnerar information om vilka programtyper av som tillhandahålls eller håller på att tillhandahålls i Service Fabric-klustret. De här resultaten är programtyper vars namn matchar exakt angiven som parameter och som uppfyller de angivna frågeparametrarna. Alla versioner av programtyp som matchar namnet på programmet returneras för varje version som returneras som en typ av program. Svaret innehåller namn, version, status och annan information om vilken programtyp. Detta är en växlingsbar fråga, vilket innebär att som om inte alla programtyper får plats i en sida, returneras en sida med resultat samt en fortsättningstoken som kan användas för att hämta nästa sida. Till exempel är om det finns 10 programtyper men en sida passar bara de första tre programtyperna eller om max resultatuppsättningen till 3, 3 returneras. Hämta de efterföljande sidorna med returnerade fortsättningstoken i nästa fråga för att komma åt resten av resultaten. En tom fortsättningstoken returneras om det finns inga kommande sidor.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs] | Namnet på programtyp. |
| --typ programversion | Versionen av programtypen. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --Utelämna applikationsparametrarna | Flagga som anger om programmet parametrar kommer att uteslutas från resultatet. |
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

## <a name="sfctl-application-type-list"></a>sfctl typ-lista
Hämtar listan över programtyper i Service Fabric-klustret.

Returnerar information om vilka programtyper av som tillhandahålls eller håller på att tillhandahålls i Service Fabric-klustret. Varje version av programtyp returneras som ett program. Svaret innehåller namn, version, status och annan information om vilken programtyp. Detta är en växlingsbar fråga, vilket innebär att som om inte alla programtyper får plats i en sida, returneras en sida med resultat samt en fortsättningstoken som kan användas för att hämta nästa sida. Till exempel är om det finns 10 programtyper men en sida passar bara de första tre programtyperna eller om max resultatuppsättningen till 3, 3 returneras. Hämta de efterföljande sidorna med returnerade fortsättningstoken i nästa fråga för att komma åt resten av resultaten. En tom fortsättningstoken returneras om det finns inga kommande sidor.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typ-definition-typ-filter | Används för att filtrera på ApplicationTypeDefinitionKind, vilket är den mekanism som används för att definiera en typ för Service Fabric-program.  <br> -Standard - standardvärde som utför samma funktion som du väljer ”alla”. Värdet är 0.  <br> -Alla - Filter som matchar indata med ett ApplicationTypeDefinitionKind-värde. Värdet är 65535.  <br> -ServiceFabricApplicationPackage - Filter som matchar indata med ApplicationTypeDefinitionKind värdet ServiceFabricApplicationPackage. Värdet är 1.  <br> -Skriv - Filter som matchar indata med ApplicationTypeDefinitionKind värdet Compose. Värdet är 2. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --Utelämna applikationsparametrarna | Flagga som anger om programmet parametrar kommer att uteslutas från resultatet. |
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

## <a name="sfctl-application-unprovision"></a>sfctl program avetablera
Tar bort eller Avregistrerar en Service Fabric programtyp från klustret.

Tar bort eller Avregistrerar en Service Fabric programtyp från klustret. Den här åtgärden kan endast utföras om alla programinstanser av programmet har tagits bort. När programtypen är avregistrerade, kan inga nya programinstanser skapas för den här typen av program.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs] | Namnet på programtyp. |
| --program-type-version [krävs] | Versionen av programtyp som definierats i programmanifestet. |
| --async-parameter | Flagga som anger huruvida avetablera ska ske asynkront. Om värdet är true och returnerar avetablera igen fortsätter när begäran har godkänts av systemet och avetablera åtgärden utan timeout-gränsen. Standardvärdet är false. Vi rekommenderar dock ange värdet till true för stora programpaket som etablerades. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade"></a>uppgradering av sfctl programmet
Börja uppgradera ett program i Service Fabric-klustret.

Validerar angivna uppgradera applikationsparametrarna och börja uppgradera programmet om parametrarna är giltiga. Observera att uppgradera beskrivning ersätter den befintliga Programbeskrivningen. Det innebär att om parametrarna inte har angetts, befintliga parametrar på program att skrivas över med listan tom parametrar. Detta resulterar i program som använder standardvärdet för parametrarna från programmanifestet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. <br><br> Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den '\~' tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ' myapp\~app1' i 6.0 + och ' myapp/app1 ”i tidigare versioner. |
| --programversion [krävs] | Målversionen för programmet. |
| --Parametrar [krävs] | En JSON-kodade lista över program parametern åsidosättningar för att användas när du uppgraderar programmet. |
| – standard-tjänsten-hälsa-policy | JSON-kodade specificering av hälsoprincipen används som standard för att utvärdera hälsan för en tjänsttyp. |
| --åtgärd vid | Åtgärden som ska utföras vid en uppgradering av övervakade övervakning princip- eller principöverträdelser. |
| --force-restart | Starta tvång om processer under uppgraderingen även när code-versionen inte har ändrats. |
| --hälsa-check-retry-timeout | Hur lång tid att försöka hälsa utvärderingar när programmet eller kluster är felfri innan den misslyckade åtgärden körs. Mätt i millisekunder.  Som standard\: PT0H10M0S. |
| --health check-stabil-varaktighet | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter till nästa uppgraderingsdomän. Mätt i millisekunder.  Som standard\: PT0H2M0S. |
| --health check-vänta-varaktighet | Mängden väntetiden när du har slutfört en uppgraderingsdomän innan du tillämpar hälsoprinciper. Mätt i millisekunder.  Som standard\: 0. |
| --max ohälsosamt appar | Maximalt tillåten procentandel av felaktiga distribuerade program. Representeras som ett tal mellan 0 och 100. |
| --läge | Det läge som används för att övervaka hälsa under en uppgradering.  Som standard\: UnmonitoredAuto. |
| --replik-set-check-timeout | Längsta tid att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det uppstår oväntade problem. Mäts i sekunder. |
| --tjänst hälsoprincip | JSON-kodade karta med tjänsten typen hälsoprincip per tjänstnamnet typen. Kartan är tom vara standard. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --uppgraderingen domäntidsgräns | Hur lång tid varje uppgraderingsdomänen måste slutföras innan FailureAction körs. Mätt i millisekunder.  Som standard\: P10675199DT02H48M05.4775807S. |
| --uppgraderingen-timeout | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs. Mätt i millisekunder.  Som standard\: P10675199DT02H48M05.4775807S. |
| --warning-as-error | Behandla hälsovarningar i utvärderingen med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade-resume"></a>Fortsätt med sfctl program uppgradering
Återupptar uppgradera ett program i Service Fabric-klustret.

Återupptar ett oövervakade manuell Service Fabric-program uppgradera. Service Fabric uppgraderar en domän i taget. För oövervakade manuell uppgradering när Service Fabric är klar en uppgraderingsdomän väntar den att anropa denna API innan du fortsätter till nästa uppgraderingsdomän.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --uppgraderingen-domännamn [krävs] | Namnet på uppgraderingsdomänen där du vill fortsätta uppgraderingen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl program uppgraderingen-återställning
Startar återställer för närvarande löpande uppgradering av ett program i Service Fabric-klustret.

Startar återställer det aktuella programmet uppgradera till föregående version. Detta API kan endast användas för att återställa den aktuella pågående uppgradering som återställer till en ny version. Om programmet inte håller på att uppgraderas kan du använda StartApplicationUpgrade API för att uppgradera den till önskade versionen, inklusive att återställa en tidigare version.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade-status"></a>sfctl uppgraderingen-programstatus
Hämtar information för den senaste uppgraderingen utförs på det här programmet.

Returnerar information om tillståndet för den senaste uppgraderingen program tillsammans med information för att underlätta felsökning programproblem hälsa.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-application-upload"></a>sfctl program överför
Kopiera ett Service Fabric-programpaket till image store.

Du kan också visa överföringen pågår för varje fil i paketet. Ladda upp förloppet skickas till `stderr`.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --sökväg [krävs] | Sökvägen till lokala programpaket. |
| --avbildningsarkiv sträng | Mål-avbildningen lagra för att överföra programpaket till.  Som standard\: infrastrukturresurser\:Avbildningsarkiv. |
| – Visa förlopp | Visa förlopp för överföring av filen för stora paket. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |



## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
