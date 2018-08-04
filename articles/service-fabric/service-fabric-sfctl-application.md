---
title: Azure Service Fabric CLI - sfctl-program | Microsoft Docs
description: Beskriver Service Fabric CLI-kommandon sfctl program.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 40ec204f105b32c8b7d9e2dda6f6f3c3023b2d44
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495466"
---
# <a name="sfctl-application"></a>sfctl application
Skapa, ta bort och hantera program och program.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar ett Service Fabric-program med den angivna beskrivningen. |
| delete | Tar bort ett befintligt Service Fabric-program. |
| distribuerat | Hämtar information om ett program som distribueras på en Service Fabric-nod. |
| distribuerat hälsotillstånd | Hämtar information om hälsotillståndet för ett program som distribuerats på en Service Fabric-nod. |
| distribuerat lista | Hämtar listan över program som distribueras på en Service Fabric-nod. |
| hälsotillstånd | Hämtar hälsotillståndet för service fabric-program. |
| info | Hämtar information om Service Fabric-program. |
| lista | Hämtar listan över program som har skapats i Service Fabric-klustret och som matchar de angivna filtren har använts. |
| läsa in | Hämtar att läsa in information om Service Fabric-program. |
| Manifest | Hämtar manifestet som beskriver en typ av program. |
| etablera | Etablerar eller registrerar skriver ett Service Fabric-program till klustret med hjälp av .sfpkg-paketet i extern lagring eller med hjälp av programpaketet i avbildningsarkivet. |
| rapportera hälsa | Skickar en hälsorapport på Service Fabric-program. |
| typ | Hämtar listan över typer av program i Service Fabric-klustret som matchar exakt det angivna namnet. |
| typ-list | Hämtar listan över typer av program i Service Fabric-klustret. |
| avetablera | Tar bort eller Avregistrerar en Service Fabric-programtypen från klustret. |
| uppgradering | Börjar uppgradera ett program i Service Fabric-klustret. |
| uppgraderingen återuppta | Återupptar uppgradera ett program i Service Fabric-klustret. |
| uppgraderingen-återställning | Börjar återställa för närvarande pågående uppgradering av ett program i Service Fabric-klustret. |
| uppgraderingen-status | Hämtar information för den senaste uppgraderingen utförs på det här programmet. |
| upload | Kopiera ett Service Fabric-programpaket till avbildningsarkivet. |

## <a name="sfctl-application-create"></a>sfctl programmet skapa
Skapar ett Service Fabric-program med den angivna beskrivningen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| [krävs] – appens namn | Namnet på programmet, inklusive den ”fabric\:” URI-schema. |
| [krävs] – app-typ | Programnamnet för typen som hittades i manifestet. |
| [krävs] – app-version | Versionen av programtyp som definierats i manifestet. |
| --max nodantal | Det maximala antalet noder där Service Fabric kommer reserverad kapacitet för det här programmet. Observera att detta inte innebär att tjänster i det här programmet kommer att placeras på alla dessa noder. |
| – mått | En JSON-kodad lista över program kapacitet mått beskrivningar. Ett mått har definierats som ett namn, som är associerade med en uppsättning kapaciteter för varje nod som programmet finns på. |
| --min nodantal | Det minsta antalet noder där Service Fabric kommer reserverad kapacitet för det här programmet. Observera att detta inte innebär att tjänster i det här programmet kommer att placeras på alla dessa noder. |
| – parametrar | En JSON-kodad lista över program parametern åsidosättningar för att användas när du skapar programmet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-delete"></a>sfctl application delete
Tar bort ett befintligt Service Fabric-program.

Ett program måste skapas innan den kan tas bort. Tar bort ett program tas bort alla tjänster som ingår i programmet. Som standard försöker Service Fabric att stänga service repliker på ett korrekt sätt och sedan ta bort tjänsten. Men om en tjänst som har problem stänger repliken utan problem, borttagningen kan ta lång tid eller fastnar. Använder du flaggan valfritt ForceRemove att hoppa över korrekt Stäng sekvensen och kernelpaketet ta bort programmet och alla dess tjänster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| – force-ta bort | Ta bort en Service Fabric-program eller tjänst kernelpaketet utan att gå igenom de avslutning. Den här parametern kan användas för att kernelpaketet ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt slutet av repliker. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-deployed"></a>sfctl program distribuerade
Hämtar information om ett program som distribueras på en Service Fabric-nod.

Den här frågan returnerar information om programmet om det program-ID som angetts för system-program. Resultaten omfatta distribuerade program i aktiv, aktivera och hämta tillstånd. Den här frågan kräver att namnet på noden motsvarar en nod i klustret. Frågan misslyckas om det angivna nodnamnet inte refererar till någon aktiv Service Fabric-noder i klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| – Inkludera hälsotillstånd | Inkludera hälsotillståndet för en entitet. Om den här parametern är false eller har inte angetts, är hälsotillståndet returneras ”okänt”. När går inställd på Sant frågan parallellt till noden och tjänsten för hälsotillstånd system innan resultatet slås samman. Därför kan frågan är dyrare och kan ta längre tid. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-deployed-health"></a>sfctl program distribuerade hälsa
Hämtar information om hälsotillståndet för ett program som distribuerats på en Service Fabric-nod.

Hämtar information om hälsotillståndet för ett program som distribuerats på en Service Fabric-nod. Använd EventsHealthStateFilter att du kan också filtrera för objektsamlingen HealthEvent rapporteras på det distribuerade programmet som baseras på hälsotillståndet. Använd DeployedServicePackagesHealthStateFilter för att du kan också filtrera för DeployedServicePackageHealth barn baseras på hälsotillståndet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --DEPLOYED-Service-Packages-Health-State-filter | Tillåter filtrering av distribuerade tjänsten paketet hälsotillstånd tillstånd objekten som returneras i resultatet av distribuerade programmets hälsotillstånd fråga baserat på deras hälsotillstånd. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast distribueras service paket som matchar filtret som returneras. Alla distribuerade tjänstpaket som används för att analysera sammanställda hälsotillståndet för det distribuerade programmet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för servicepaket med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --Exkludera hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-deployed-list"></a>sfctl program distribuerade-list
Hämtar listan över program som distribueras på en Service Fabric-nod.

Hämtar listan över program som distribueras på en Service Fabric-nod. Resultatet inkluderar inte information om distribuerade systemprogram om inte explicit efterfrågas av ID. Resultaten omfatta distribuerade program i aktiv, aktivera och hämta tillstånd. Den här frågan kräver att namnet på noden motsvarar en nod i klustret. Frågan misslyckas om det angivna nodnamnet inte refererar till någon aktiv Service Fabric-noder i klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --fortsättningstoken | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| – Inkludera hälsotillstånd | Inkludera hälsotillståndet för en entitet. Om den här parametern är false eller har inte angetts, är hälsotillståndet returneras ”okänt”. När går inställd på Sant frågan parallellt till noden och tjänsten för hälsotillstånd system innan resultatet slås samman. Därför kan frågan är dyrare och kan ta längre tid. |
| --max resultat | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan mindre än de angivna maximalt antal resultat om de inte passar i meddelandet enligt storleksbegränsningar max meddelande definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlade frågan så många resultat som möjligt som passar in i svarsmeddelandet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-health"></a>sfctl programmets hälsotillstånd
Hämtar hälsotillståndet för service fabric-program.

Returnerar hälsoavsökning tillståndet för service fabric-program. Svaret rapporterar Ok, fel eller varningar hälsotillstånd. Om entiteten inte hittas i health store, returneras fel.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --deployed-applications-health-state-filter | Tillåter filtrering av distribuerade program hälsotillstånd tillstånd objekt returneras i resultatet av application health fråga baserat på deras hälsotillstånd. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast distribuerade program som matchar filtret kommer att returneras. Alla distribuerade program används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för distribuerade program med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --Exkludera hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --services-health-state-filter | Tillåter filtrering av services health tillstånd-objekt som returneras i resultatet av services health-frågan baserat på deras hälsotillstånd. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast de tjänster som matchar filtret returneras. Alla tjänster används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras sedan hälsotillståndet för tjänster med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-info"></a>sfctl programinformation
Hämtar information om Service Fabric-program.

Returnerar information om det program som har skapats eller håller på att skapas i Service Fabric-klustret och vars namn matchar det som anges som parameter. Svaret innehåller namn, typ, status, parametrar och annan information om programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Exkludera programparametrar | Flagga som anger om programparametrar kommer att uteslutas från resultatet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-list"></a>sfctl programlista
Hämtar listan över program som har skapats i Service Fabric-klustret och som matchar de angivna filtren har använts.

Hämtar information om vilka program som har skapats eller håller på att skapas i Service Fabric-kluster och matchar de angivna filtren. Svaret innehåller namn, typ, status, parametrar och annan information om programmet. Om program som inte får plats på en sida, returneras en resultatsida samt ett fortsättningstoken som kan användas för att hämta nästa sida. Filter ApplicationTypeName och ApplicationDefinitionKindFilter kan inte anges samtidigt.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --application-definition-kind-filter | Används för att filtrera på ApplicationDefinitionKind, vilket är den mekanism som används för att definiera ett Service Fabric-program.  <br> -Standard - standardvärde som utför samma funktion som att välja ”alla”. Värdet är 0.  <br> -Alla - Filter som matchar indata med ett ApplicationDefinitionKind-värde. Värdet är 65535.  <br> -ServiceFabricApplicationDescription - Filter som matchar indata med ApplicationDefinitionKind värdet ServiceFabricApplicationDescription. Värdet är 1.  <br> -Skriv - Filter som matchar indata med ApplicationDefinitionKind värde Skriv. Värdet är 2. |
| --programmets typnamn | Programnamnet för typen som används för att filtrera program att fråga efter. Det här värdet får inte innehålla versionen av programtyp. |
| --fortsättningstoken | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --Exkludera programparametrar | Flagga som anger om programparametrar kommer att uteslutas från resultatet. |
| --max resultat | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan mindre än de angivna maximalt antal resultat om de inte passar i meddelandet enligt storleksbegränsningar max meddelande definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlade frågan så många resultat som möjligt som passar in i svarsmeddelandet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-load"></a>sfctl programinläsning
Hämtar att läsa in information om Service Fabric-program.

Returnerar load-information om programmet som har skapats eller håller på att skapas i Service Fabric-klustret och vars namn matchar det som anges som parameter. Svaret innehåller namnet, lägsta antal noder, högsta antalet noder, antalet noder som programmet några för närvarande och application load mått information om programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-manifest"></a>sfctl programmanifest
Hämtar manifestet som beskriver en typ av program.

Svaret innehåller programmanifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-typnamn [krävs] | Namnet på vilken typ av program. |
| –--version av programtyp [krävs] | Versionen av programtypen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-provision"></a>sfctl application provision
Etablerar eller registrerar skriver ett Service Fabric-program till klustret med hjälp av .sfpkg-paketet i extern lagring eller med hjälp av programpaketet i avbildningsarkivet.

Etablerar en programtyp i Service Fabric med klustret. Detta krävs innan några nya program kan instansieras. Etableringsåtgärden kan utföras antingen på programpaketet som anges av relativePathInImageStore, eller med hjälp av URI: N för den externa .sfpkg. Såvida inte--externa etablera har angetts, kommer det här kommandot förväntar sig bild store etablera.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-paketet-download-uri | Sökvägen till programpaketet '.sfpkg' varifrån programpaketet kan hämtas med HTTP eller HTTPS-protokoll. <br><br> Lagra endast för etablera typer externa. Programpaketet kan lagras i en extern lagring som erbjuder GET-åtgärden för att hämta filen. Protokoll som stöds är HTTP och HTTPS och sökvägen måste tillåta läsåtkomst. |
| ---typ-build-programsökväg | För att etablera typ avbildningsarkivet endast. Den relativa sökvägen för programpaket i avbildningsarkivet anges under tidigare överföringen. |
| --programmets typnamn | Lagra endast för etablera typer externa. Namnet på programmet representerar namnet på vilken typ av program finns i programmanifestet. |
| --programmets Typversion | Lagra endast för etablera typer externa. Versionen av programtyp representerar versionen av programtyp som hittades i manifestet. |
| --externa etablera | Platsen där programpaket kan registreras eller etableras. Anger att tillhandahållandet av programpaket som tidigare har överförts till en extern lagring. Programpaketet slutar med tillägget *.sfpkg. |
| --no-wait- | Anger huruvida etablering ska ske asynkront. <br><br> Om värdet är true, etableringsåtgärden returnerar fortsätter när begäran har godkänts av systemet och etableringsåtgärden utan någon timeout-gränsen. Standardvärdet är FALSKT. För stora programpaket rekommenderar vi ställa in värdet till true. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-report-health"></a>sfctl application report-health
Skickar en hälsorapport på Service Fabric-program.

Rapporterar hälsotillståndet för det angivna Service Fabric-programmet. Rapporten måste innehålla information om orsaken hälsorapport och egenskapen som har rapporterats. Rapporten skickas till en Service Fabric gateway programmet, som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen, men avvisats av health store efter extra valideringen. Health store kan till exempel avvisa rapporten på grund av en ogiltig parameter, t.ex. ett sekvensnummer som är inaktuella. Om du vill se om rapporten har tillämpats i health store, få programmets hälsotillstånd och kontrollera att rapporten visas.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. <br><br> Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den '\~' tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1 ', programidentiteten skulle vara” myapp\~app1' i 6.0 + och ”myapp/app1' i tidigare versioner. |
| --health-egenskapen [krävs] | Egenskapen för hälsoinformation. <br><br> En entitet kan ha rapporter om hälsotillstånd för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport flexibiliteten att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan till exempel övervaka status för tillgängliga disken på en nod, så att den kan rapportera ”AvailableDisk”-egenskap på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. Dessa rapporter behandlas i health store, som separata health-händelser för den angivna noden. Tillsammans med målentiteten identifierar egenskapen hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden omfattar\: ”ogiltig”, ”Ok”, ”varning”, ”fel”, ”okänd”. |
| --käll-id [krävs] | Källnamn som identifierar/klientsystemet/watchdog-komponenten som genererat hälsoinformation. |
| – Beskrivning | Beskrivning av hälsoinformation. <br><br> Den motsvarar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4 096 tecken. Om strängen inte trunkeras den automatiskt. När trunkeras, de sista tecknen i beskrivningen innehåller en markör ”[trunkerat]” och totala storleken på målsträngen är 4 096 tecken. Förekomst av markören anger för användare att trunkering inträffade. Observera att när trunkeras, beskrivningen har mindre än 4 096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till en Service Fabric gateway programmet, som vidarebefordrar till health store. Om Immediate anges till SANT, skickas rapporten direkt från HTTP-Gateway till health store, oavsett inställningarna i fabric-klient som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas skicka rapporten fortfarande, till exempel om HTTP-Gateway är stängd eller meddelandet når inte gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningarna hälsotillstånd från HTTP-Gateway. Det kan därför batchhanteras enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom den tillåter hälsotillstånd klienten att optimera reporting meddelanden hälsoarkivet, samt för bearbetning av hälsotillstånd. Som standard skickas rapporter inte omedelbart. |
| – ta bort när-har upphört | Värde som anger om rapporten tas bort från health store när den upphör att gälla. <br><br> Om värdet är true, rapporten tas bort från health store när den upphör att gälla. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). På så sätt kan är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnumret rapporten används av health store för att identifiera inaktuella rapporter. Om inte anges genereras ett sekvensnummer automatiskt av hälsotillstånd klienten när du lägger till en rapport. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --ttl | Den tid som den här hälsorapport är giltig. Det här fältet använder ISO8601-format för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, bör de skicka rapporter med frekvens som är högre än TTL-värde. Om klienterna rapporterar på övergång, kan de ange time to live till obegränsad. När TTL-värde upphör att gälla hälsohändelsen som innehåller hälsoinformation är antingen tas bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten TTL-värde standardvärdet är oändligt värde. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-type"></a>sfctl programtyp
Hämtar listan över typer av program i Service Fabric-klustret som matchar exakt det angivna namnet.

Returnerar information om vilka programtyper av som har etablerats eller håller på att håller på att etableras i Service Fabric-klustret. De här resultaten är programtyper vars namn matchar exakt det som anges som parameter och som överensstämmer med de angivna frågeparametrarna. Alla versioner av vilken typ av program som matchar namnet på programmet som returneras med varje version som returneras som en programtyp. Svaret innehåller namn, version, status och annan information om vilken typ av program. Det här är en växlingsbar fråga, vilket innebär att som om inte alla typerna av program som får plats på en sida, returneras en resultatsida samt ett fortsättningstoken som kan användas för att hämta nästa sida. Till exempel returneras om det finns 10 programtyper men en sida passar bara de första tre programtyperna, eller om max resultat är inställd på 3, sedan tre. Hämta de efterföljande sidorna med returnerade fortsättningstoken i nästa fråga för att komma åt resten av resultaten. En tom fortsättningstoken returneras om det finns inga efterföljande sidor.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-typnamn [krävs] | Namnet på vilken typ av program. |
| --programmets Typversion | Versionen av programtypen. |
| --fortsättningstoken | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --Exkludera programparametrar | Flagga som anger om programparametrar kommer att uteslutas från resultatet. |
| --max resultat | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan mindre än de angivna maximalt antal resultat om de inte passar i meddelandet enligt storleksbegränsningar max meddelande definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlade frågan så många resultat som möjligt som passar in i svarsmeddelandet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-type-list"></a>sfctl typ-programlista
Hämtar listan över typer av program i Service Fabric-klustret.

Returnerar information om vilka programtyper av som har etablerats eller håller på att håller på att etableras i Service Fabric-klustret. Varje version av programtyp returneras som en programtyp. Svaret innehåller namn, version, status och annan information om vilken typ av program. Det här är en växlingsbar fråga, vilket innebär att som om inte alla typerna av program som får plats på en sida, returneras en resultatsida samt ett fortsättningstoken som kan användas för att hämta nästa sida. Till exempel returneras om det finns 10 programtyper men en sida passar bara de första tre programtyperna, eller om max resultat är inställd på 3, sedan tre. Hämta de efterföljande sidorna med returnerade fortsättningstoken i nästa fråga för att komma åt resten av resultaten. En tom fortsättningstoken returneras om det finns inga efterföljande sidor.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-typ-definition-typ-filter | Används för att filtrera på ApplicationTypeDefinitionKind som är den mekanism som används för att definiera en Service Fabric-programtypen.  <br> -Standard - standardvärde som utför samma funktion som att välja ”alla”. Värdet är 0.  <br> -Alla - Filter som matchar indata med ett ApplicationTypeDefinitionKind-värde. Värdet är 65535.  <br> -ServiceFabricApplicationPackage - Filter som matchar indata med ApplicationTypeDefinitionKind värdet ServiceFabricApplicationPackage. Värdet är 1.  <br> -Skriv - Filter som matchar indata med ApplicationTypeDefinitionKind värde Skriv. Värdet är 2. |
| --fortsättningstoken | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --Exkludera programparametrar | Flagga som anger om programparametrar kommer att uteslutas från resultatet. |
| --max resultat | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan mindre än de angivna maximalt antal resultat om de inte passar i meddelandet enligt storleksbegränsningar max meddelande definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlade frågan så många resultat som möjligt som passar in i svarsmeddelandet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-unprovision"></a>sfctl program avetablera
Tar bort eller Avregistrerar en Service Fabric-programtypen från klustret.

Den här åtgärden kan endast utföras om alla instanser av programmet av programtypen har tagits bort. När programtypen är avregistrera kan kan inga nya instanser av programmet skapas för den här programtypen av specifika.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-typnamn [krävs] | Namnet på vilken typ av program. |
| –--version av programtyp [krävs] | Versionen av programtyp som definierats i manifestet. |
| --async-parameter | Flagga som indikerar huruvida avetablera ska ske asynkront. Om värdet är true, avetablera åtgärden returnerar fortsätter när begäran har godkänts av systemet och avetablera åtgärden utan någon timeout-gränsen. Standardvärdet är FALSKT. Vi rekommenderar dock ange värdet till true för stora programpaket som etablerades. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade"></a>sfctl Programuppgradering
Börjar uppgradera ett program i Service Fabric-klustret.

Validerar angivna programuppgraderingsparametrar och börjar uppgradera programmet om parametrarna är giltiga. Observera att uppgradera beskrivning ersätter Programbeskrivningen av befintliga. Det innebär att om parametrarna inte anges kommer befintliga parametrar på programmen skrivs med tom parameterlistan. Detta resulterar i programmet med standardvärdet för parametrar från applikationsmanifestet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. <br><br> Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den '\~' tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1 ', programidentiteten skulle vara” myapp\~app1' i 6.0 + och ”myapp/app1' i tidigare versioner. |
| --programversion [krävs] | Målversionen för programmet. |
| --Parametrar [krävs] | En JSON-kodad lista över program parametern åsidosättningar för att användas när du uppgraderar programmet. |
| – Standard-service-hälsa-policy | JSON-kodad specifikation av hälsoprincip som används som standard för att utvärdera hälsan för en typ av tjänst. |
| --åtgärd vid uppgraderingsfel | Åtgärden som ska utföras när en uppgradering av övervakade påträffar övervakning principöverträdelser för principen eller hälsotillstånd. |
| --force-restart | Starta kernelpaketet om processer under uppgraderingen även när code-versionen inte har ändrats. |
| --hälsotillstånd –--tidsgräns för ny hälsokontroll | Hur lång tid att försöka igen hälsotillstånd utvärderingar när programmet eller klustret är i feltillstånd innan den misslyckade åtgärden utförs. Mätt i millisekunder.  Standard\: PT0H10M0S. |
| –--stabil-hälsokontroll | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter du med nästa uppgraderingsdomän. Mätt i millisekunder.  Standard\: PT0H2M0S. |
| –--wait-hälsokontroll | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du tillämpar hälsoprinciper. Mätt i millisekunder.  Standard\: 0. |
| --max felaktiga appar | Högsta tillåtna procentandel av distribuerade program som är felaktiga. Visas som ett tal mellan 0 och 100. |
| --läge | Det läge som används för att övervaka hälsa under en löpande uppgradering.  Standard\: UnmonitoredAuto. |
| --replik-set-kontroll-timeout | Längsta tid att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns ett oväntat problem. Mätt i sekunder. |
| --service hälsoprincip | JSON-kodad karta med tjänsten typ hälsoprincip per typ tjänstnamn. Kartan är tom vara standard. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --uppgraderingen domäntidsgräns | Hur lång tid varje domän har slutförts innan FailureAction körs. Mätt i millisekunder.  Standard\: P10675199DT02H48M05.4775807S. |
| --Tidsgräns för uppgradering | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs. Mätt i millisekunder.  Standard\: P10675199DT02H48M05.4775807S. |
| --warning-as-error | Hantera hälsotillståndsvarningar i utvärderingen med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl program uppgraderingen-återuppta
Återupptar uppgradera ett program i Service Fabric-klustret.

Återupptar uppgradera för en obevakad manuell Service Fabric-program. Service Fabric uppgraderar en uppgraderingsdomän i taget. För oövervakade manuella uppgraderingar, när Service Fabric är klar en uppgraderingsdomän väntar den att anropa detta API innan du fortsätter med nästa uppgraderingsdomän.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --uppgraderingen-domain-name [krävs] | Namnet på uppgraderingsdomänen där du vill fortsätta uppgraderingen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl program uppgraderingen-återställning
Börjar återställa för närvarande pågående uppgradering av ett program i Service Fabric-klustret.

Börjar återställa det aktuella programmet uppgradera till den tidigare versionen. Detta API kan endast användas för att återställa aktuella pågående uppgraderingen som är aktiv framåt till ny version. Om programmet inte uppgraderas just nu använder du StartApplicationUpgrade API för att uppgradera den till önskade versionen, inklusive att återställa till en tidigare version.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-upgrade-status"></a>sfctl uppgraderingen-programstatus
Hämtar information för den senaste uppgraderingen utförs på det här programmet.

Returnerar information om status för senaste Programuppgradering tillsammans med information för att underlätta felsökning hälsoproblem för programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-application-upload"></a>ladda upp sfctl-program
Kopiera ett Service Fabric-programpaket till avbildningsarkivet.

Du kan också visa uppladdningsförloppet för varje fil i paketet. Ladda upp förloppet skickas till `stderr`.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --sökväg [krävs] | Sökvägen till lokala programpaket. |
| --imagestore-sträng | Mål-avbildningen lagra för att överföra programpaket till.  Standard\: fabric\:ImageStore. |
| – Visa förlopp | Visa förlopp för uppladdning av filen för stora paket. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
