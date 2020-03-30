---
title: Azure Service Fabric CLI- sfctl-program
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista över kommandon för att hantera program.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b4e1066bba1db387c9dc0600bc55522f0b5fe897
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906209"
---
# <a name="sfctl-application"></a>sfctl application
Skapa, ta bort och hantera program och programtyper.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar ett Service Fabric-program med den angivna beskrivningen. |
| delete | Tar bort ett befintligt Service Fabric-program. |
| Distribuerat | Hämtar information om ett program som distribueras på en Service Fabric-nod. |
| distribuerad hälsa | Hämtar information om hälsotillstånd för ett program som distribueras på en Service Fabric-nod. |
| distribuerad lista | Hämtar listan över program som distribueras på en Service Fabric-nod. |
| Hälsa | Hämtar hälsotillståndet för programmet för serviceinfrastruktur. |
| information | Hämtar information om ett Service Fabric-program. |
| lista | Hämtar listan över program som skapats i Service Fabric-klustret som matchar de angivna filtren. |
| läsa in | Hämtar inläsningsinformation om ett Service Fabric-program. |
| manifest | Hämtar manifestet som beskriver en programtyp. |
| Bestämmelse | Avsättningar eller registrerar en tjänst fabric-programtyp med klustret med hjälp av paketet ".sfpkg" i det externa arkivet eller med hjälp av programpaketet i avbildningsarkivet. |
| rapport-hälsa | Skickar en hälsorapport om programmet Service Fabric. |
| typ | Hämtar listan över programtyper i Service Fabric-klustret som matchar exakt det angivna namnet. |
| typlista | Hämtar listan över programtyper i Service Fabric-klustret. |
| oetablera | Tar bort eller avregistrerar en tjänst fabric-programtyp från klustret. |
| uppgradera | Startar uppgradering av ett program i Service Fabric-klustret. |
| uppgradering-återuppta | Återupptar uppgraderingen av ett program i service fabric-klustret. |
| återställning av uppgradering | Startar att återställa den pågående uppgraderingen av ett program i service fabric-klustret. |
| uppgraderingsstatus | Hämtar information om den senaste uppgraderingen som utförts för det här programmet. |
| upload | Kopiera ett Programpaket för Service Fabric till bildarkivet. |

## <a name="sfctl-application-create"></a>sfctl ansökan skapa
Skapar ett Service Fabric-program med den angivna beskrivningen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-name [Obligatoriskt] | Namnet på programmet, inklusive "fabric"\:URI-schemat. |
| --app-typ [Obligatoriskt] | Programtypens namn som finns i programmanifestet. |
| --app-version [Obligatoriskt] | Den version av programtypen som definieras i programmanifestet. |
| --max-nod-antal | Det maximala antalet noder där Service Fabric reserverar kapacitet för det här programmet. Observera att detta inte betyder att tjänsterna i denna ansökan kommer att placeras på alla dessa noder. |
| --mått | En JSON-kodad lista över beskrivningar av programkapacitetsmått. Ett mått definieras som ett namn som associeras med en uppsättning kapaciteter för varje nod som programmet finns på. |
| --min-nod-antal | Det minsta antalet noder där Service Fabric reserverar kapacitet för det här programmet. Observera att detta inte betyder att tjänsterna i denna ansökan kommer att placeras på alla dessa noder. |
| --parametrar | En JSON-kodad lista över programparameter åsidosättningar som ska tillämpas när programmet skapas. |
| --timeout -t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-delete"></a>sfctl ansökan bort
Tar bort ett befintligt Service Fabric-program.

Ett program måste skapas innan det kan tas bort. Om du tar bort ett program tas alla tjänster som ingår i programmet bort. Som standard försöker Service Fabric stänga tjänstrepliker på ett graciöst sätt och sedan ta bort tjänsten. Men om en tjänst har problem med att stänga repliken på ett smidigt sätt kan borttagningsåtgärden ta lång tid eller fastna. Använd den valfria ForceRemove-flaggan för att hoppa över den graciösa stängningssekvensen och kraftfullt ta bort programmet och alla dess tjänster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --force-remove | Ta bort ett Service Fabric-program eller en tjänst med kraft utan att gå igenom den graciösa avstängningssekvensen. Den här parametern kan användas för att kraftfullt ta bort ett program eller en tjänst som tar bort är tidsgränsen på grund av problem i tjänstkoden som förhindrar graciös stängning av repliker. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-deployed"></a>sfctl-program har distribuerats
Hämtar information om ett program som distribueras på en Service Fabric-nod.

Den här frågan returnerar systemprograminformation om det angivna program-ID:t är för systemprogram. Resultaten omfattar distribuerade program i aktiva, aktiverande och nedladdningsbara tillstånd. Den här frågan kräver att nodnamnet motsvarar en nod i klustret. Frågan misslyckas om det angivna nodnamnet inte pekar på några aktiva Service Fabric-noder i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --include-health-state | Inkludera hälsotillståndet för en entitet. Om den här parametern är falsk eller inte angiven, är hälsotillståndet som returneras "Okänd". När den är inställd på true går frågan parallellt med noden och hälsosystemtjänsten innan resultaten slås samman. Därför är frågan dyrare och kan ta längre tid. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-deployed-health"></a>sfctl-program distribuerad-hälsa
Hämtar information om hälsotillstånd för ett program som distribueras på en Service Fabric-nod.

Hämtar information om hälsotillstånd för ett program som distribueras på en Service Fabric-nod. Använd EventsHealthStateFilter för att filtrera för insamlingen av HealthEvent-objekt som rapporterats på det distribuerade programmet baserat på hälsotillstånd. Använd DeployedServicePackagesHealthStateFilter för att eventuellt filtrera efter DistribueradeServicePackageHealth-underordnade baserat på hälsotillstånd.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --deployed-service-packages-health-state-filter | Tillåter filtrering av hälsotillståndsobjekt för distribuerade tjänstpaket som returneras till resultatet av den distribuerade programhälsofrågan baserat på deras hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast distribuerade tjänstpaket som matchar filtret returneras. Alla distribuerade tjänstpaket används för att utvärdera det aggregerade hälsotillståndet för det distribuerade programmet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av operatorn "ELLER". Om värdet till exempel är 6 returneras hälsotillståndet för tjänstpaket med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --events-health-state-filter | Gör det möjligt att filtrera samlingen av HealthEvent-objekt som returneras baserat på hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av operatorn "ELLER". Om det angivna värdet till exempel är 6 returneras alla händelser med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --exklude-hälso-statistik | Anger om hälsostatistiken ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälsotillståndet Ok, Varning och Fel. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-deployed-list"></a>sfctl-program distribuerad lista
Hämtar listan över program som distribueras på en Service Fabric-nod.

Hämtar listan över program som distribueras på en Service Fabric-nod. Resultaten innehåller inte information om distribuerade systemprogram om inte uttryckligen efterfrågas av ID. Resultaten omfattar distribuerade program i aktiva, aktiverande och nedladdningsbara tillstånd. Den här frågan kräver att nodnamnet motsvarar en nod i klustret. Frågan misslyckas om det angivna nodnamnet inte pekar på några aktiva Service Fabric-noder i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --include-health-state | Inkludera hälsotillståndet för en entitet. Om den här parametern är falsk eller inte angiven, är hälsotillståndet som returneras "Okänd". När den är inställd på true går frågan parallellt med noden och hälsosystemtjänsten innan resultaten slås samman. Därför är frågan dyrare och kan ta längre tid. |
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

## <a name="sfctl-application-health"></a>sfctl ansökan hälsa
Hämtar hälsotillståndet för programmet för serviceinfrastruktur.

Returnerar hälsotillståndet för tjänstinfrastrukturprogrammet. Svaret rapporterar hälsotillståndet Ok, Error eller Warning. Om entiteten inte hittas i hälsoarkivet returneras Fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --deployed-applications-health-state-filter | Gör det möjligt att filtrera de distribuerade programhälsotillståndsobjekten som returneras i resultatet av programmets hälsofråga baserat på deras hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast distribuerade program som matchar filtret returneras. Alla distribuerade program används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av bitvis "ELLER"-operator. Om värdet till exempel är 6 returneras hälsotillståndet för distribuerade program med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --events-health-state-filter | Gör det möjligt att filtrera samlingen av HealthEvent-objekt som returneras baserat på hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av operatorn "ELLER". Om det angivna värdet till exempel är 6 returneras alla händelser med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --exklude-hälso-statistik | Anger om hälsostatistiken ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälsotillståndet Ok, Varning och Fel. |
| --services-health-state-filter | Tillåter filtrering av tjänsthälsotillståndsobjekt som returneras i resultatet av hälsofrågan för tjänster baserat på deras hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast tjänster som matchar filtret returneras. Alla tjänster används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av bitvis "ELLER"-operator. Om värdet till exempel är 6 returneras hälsotillståndet för tjänster med HealthState-värde på OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-info"></a>sfctl ansökan info
Hämtar information om ett Service Fabric-program.

Returnerar informationen om programmet som skapades eller pågår för att skapas i service fabric-klustret och vars namn matchar den som anges som parametern. Svaret innehåller namn, typ, status, parametrar och annan information om programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters -- | Flaggan som anger om programparametrar ska uteslutas från resultatet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-list"></a>sfctl-programlista
Hämtar listan över program som skapats i Service Fabric-klustret som matchar de angivna filtren.

Hämtar information om de program som har skapats eller håller på att skapas i service fabric-klustret och matcha de angivna filtren. Svaret innehåller namn, typ, status, parametrar och annan information om programmet. Om programmen inte får plats på en sida returneras en resultatsida samt en fortsättningstoken, som kan användas för att hämta nästa sida. Filter ApplicationTypeName och ApplicationDefinitionKindFilter kan inte anges samtidigt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-definition-kind-filter | Används för att filtrera på ApplicationDefinitionKind, som är den mekanism som används för att definiera ett Service Fabric-program.  <br> - Standard - Standardvärde, som utför samma funktion som att välja "Alla". Värdet är 0.  <br> - Alla - Filter som matchar indata med alla ApplicationDefinitionKind-värde. Värdet är 65535.  <br> - ServiceFabricApplicationDescription - Filter som matchar indata med ApplicationDefinitionKind värde ServiceFabricApplicationDescription. Värdet är 1.  <br> - Komponera - Filter som matchar indata med ApplicationDefinitionKind värde Komponera. Värdet är 2. |
| --application-type-name --application-type-name --application-type-name -- | Programtypnamnet som används för att filtrera programmen att fråga efter. Det här värdet bör inte innehålla programtypsversionen. |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters -- | Flaggan som anger om programparametrar ska uteslutas från resultatet. |
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

## <a name="sfctl-application-load"></a>sfctl ansökan belastning
Hämtar inläsningsinformation om ett Service Fabric-program.

Returnerar inläsningsinformationen om programmet som skapades eller som håller på att skapas i service fabric-klustret och vars namn matchar den som anges som parameter. Svaret innehåller namn, minsta noder, maximala noder, antalet noder som programmet upptar för närvarande och programinläsningsmåttinformation om programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-manifest"></a>sfctl ansökan manifest
Hämtar manifestet som beskriver en programtyp.

Svaret innehåller programmet manifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-type-name [Obligatoriskt] | Namnet på programtypen. |
| --application-type-version [Obligatoriskt] | Versionen av programtypen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-provision"></a>sfctl ansökan bestämmelse
Avsättningar eller registrerar en tjänst fabric-programtyp med klustret med hjälp av paketet ".sfpkg" i det externa arkivet eller med hjälp av programpaketet i avbildningsarkivet.

Etablerar en tjänst fabric-programtyp med klustret. Bestämmelsen krävs innan nya program kan instansieras. Etableringsåtgärden kan utföras antingen på det programpaket som anges av relativePathInImageStore eller genom att använda URI:n för den externa '.sfpkg'. Om inte --extern-etablering är inställd, kommer det här kommandot förväntar bildlagringsetablering.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --ansökan-paket-download-uri | Sökvägen till programpaketet '.sfpkg från vilken programpaketet kan hämtas med HJÄLP AV HTTP- eller HTTPS-protokoll. <br><br> Endast för avsättningssortsorta externt arkiv. Programpaketet kan lagras i ett externt arkiv som tillhandahåller GET-åtgärden för att hämta filen. Protokoll som stöds är HTTP och HTTPS, och sökvägen måste tillåta LÄS-åtkomst. |
| --application-type-build-path --application-type-build-path --application-type-build-path -- | Endast för etableringssortbildarkivet. Den relativa sökvägen för programpaketet i bildarkivet som angavs under den tidigare uppladdningsåtgärden. |
| --application-type-name --application-type-name --application-type-name -- | Endast för avsättningssortsorta externt arkiv. Programtypens namn representerar namnet på den programtyp som finns i programmanifestet. |
| --application-type-version --application-type-version --application-type-version -- | Endast för avsättningssortsorta externt arkiv. Programtypsversionen representerar versionen av programtypen som finns i programmanifestet. |
| --extern-tillhandahållande | Den plats där programpaketet kan registreras eller etableras. Anger att bestämmelsen gäller ett programpaket som tidigare har överförts till ett externt arkiv. Programpaketet avslutas med tillägget *.sfpkg. |
| --ingen-vänta | Anger om etablering ska ske asynkront. <br><br> När den är inställd på true returneras etableringsåtgärden när begäran accepteras av systemet och etableringsåtgärden fortsätter utan någon timeout-gräns. Standardvärdet är false. För stora programpaket rekommenderar vi att värdet ska vara sant. |
| --timeout -t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-report-health"></a>sfctl ansökan rapport-hälsa
Skickar en hälsorapport om programmet Service Fabric.

Rapporterar hälsotillståndet för det angivna Service Fabric-programmet. Rapporten måste innehålla information om källan till hälsorapporten och egenskapen som den rapporteras till. Rapporten skickas till ett service fabric-gatewayprogram som vidarebefordras till hälsoarkivet. Rapporten kan accepteras av gatewayen, men avvisas av hälsoarkivet efter extra validering. Hälsoarkivet kan till exempel avvisa rapporten på grund av en ogiltig parameter, till exempel ett inaktuellt sekvensnummer. Om du vill se om rapporten har tillämpats i hälsoarkivet hämtar du programmets hälsotillstånd och kontrollerar att rapporten visas.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. <br><br> Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med tecknet ' ' . Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och 'myapp/app1' i tidigare versioner. |
| --health-property [Obligatoriskt] | Egenskapen av hälsoinformationen. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning för att tillåta reporterflexibilitet att kategorisera tillståndsvillkoret som utlöser rapporten. En reporter med SourceId "LocalWatchdog" kan till exempel övervaka tillståndet för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på den noden. Samma reporter kan övervaka nodanslutningen, så att den kan rapportera en egenskap "Anslutning" på samma nod. I hälsoarkivet behandlas dessa rapporter som separata hälsohändelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen hälsoinformationen unikt. |
| --hälsotillstånd [Krävs] | Möjliga värden\: är "Ogiltig", "Ok", "Varning", "Fel", "Okänd". |
| --source-id [Obligatoriskt] | Källnamnet som identifierar komponenten klient/watchdog/system som genererade hälsoinformationen. |
| --beskrivning | Beskrivningen av hälsoinformationen. <br><br> Den representerar fritext som används för att lägga till läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Om den angivna strängen är längre trunkeras den automatiskt. När de sista tecknen i beskrivningen trunkeras innehåller de sista tecknen i beskrivningen en markör "[Trunkerad]" och den totala strängstorleken 4096 tecken. Förekomsten av markören anger för användarna att trunkering inträffade. Observera att när beskrivningen trunkeras har den mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till ett service fabric-gatewayprogram som vidarebefordras till hälsoarkivet. Om Omedelbar är inställd på true skickas rapporten omedelbart från HTTP Gateway till hälsoarkivet, oavsett de inställningar för fabric-klient som HTTP Gateway-programmet använder. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor kan det fortfarande misslyckas att skicka rapporten, till exempel om HTTP-gatewayen är stängd eller om meddelandet inte når gatewayen. Om Omedelbar är inställd på false skickas rapporten baserat på hälsoklientinställningarna från HTTP Gateway. Därför kommer den att batchas enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom hälsoklienten kan optimera hälsorapporteringsmeddelanden till hälsoarkivet samt bearbetning av hälsorapporter. Som standard skickas inte rapporter omedelbart. |
| --remove-when-expired --remove-when-expired --remove-when-expired -- | Värde som anger om rapporten tas bort från hälsoarkivet när den upphör att gälla. <br><br> Om värdet är true tas rapporten bort från hälsoarkivet när den har upphört att gälla. Om den är inställd på false behandlas rapporten som ett fel när den har upphört att gälla. Värdet för den här egenskapen är falskt som standard. När klienter rapporterar regelbundet bör de ange RemoveWhenExpired false (standard). På så sätt har reportern problem (t.ex. dödläge) och kan inte rapportera, entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som i felhälsotillstånd. |
| --sekvens-nummer | Sekvensnumret för den här hälsorapporten som en numerisk sträng. <br><br> Rapportsekvensnumret används av hälsoarkivet för att identifiera inaktuella rapporter. Om inget anges genereras ett sekvensnummer automatiskt av hälsoklienten när en rapport läggs till. |
| --timeout -t | Standard\: 60. |
| --ttl | Den varaktighet för vilken hälsorapporten är giltig. I det här fältet används ISO8601-format för att ange varaktigheten. <br><br> När klienter rapporterar regelbundet bör de skicka rapporter med högre frekvens än tid att leva. Om klienter rapporterar om övergången kan de ange att tiden ska vara oändlig. När tiden för att leva går ut tas hälsohändelsen som innehåller hälsoinformationen antingen bort från hälsoarkivet, om RemoveWhenExpired är sant eller utvärderas vid fel, om RemoveWhenExpired false. Om inget anges kan tid att leva som standard oändligt värde. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-type"></a>sfctl-programtyp
Hämtar listan över programtyper i Service Fabric-klustret som matchar exakt det angivna namnet.

Returnerar informationen om de programtyper som har etablerats eller som håller på att etableras i service fabric-klustret. Dessa resultat är av programtyper vars namn matchar exakt den som anges som parametern och som överensstämmer med de angivna frågeparametrarna. Alla versioner av programtypen som matchar programtypens namn returneras, och varje version returneras som en programtyp. Svaret innehåller namn, version, status och annan information om programtypen. Detta är en växlingsfråga, vilket innebär att om inte alla programtyper passar på en sida, returneras en resultatsida samt en fortsättningstoken, som kan användas för att hämta nästa sida. Om det till exempel finns 10 programtyper men en sida bara passar de tre första programtyperna, eller om maxresultat är satt till 3, returneras tre. Om du vill komma åt resten av resultaten hämtar du efterföljande sidor med hjälp av den returnerade fortsättningstoken i nästa fråga. En tom fortsättningstoken returneras om det inte finns några efterföljande sidor.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-type-name [Obligatoriskt] | Namnet på programtypen. |
| --application-type-version --application-type-version --application-type-version -- | Versionen av programtypen. |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters -- | Flaggan som anger om programparametrar ska uteslutas från resultatet. |
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

## <a name="sfctl-application-type-list"></a>sfctl programtyp-lista
Hämtar listan över programtyper i Service Fabric-klustret.

Returnerar informationen om de programtyper som har etablerats eller som håller på att etableras i service fabric-klustret. Varje version av en programtyp returneras som en programtyp. Svaret innehåller namn, version, status och annan information om programtypen. Detta är en växlingsfråga, vilket innebär att om inte alla programtyper passar på en sida, returneras en resultatsida samt en fortsättningstoken, som kan användas för att hämta nästa sida. Om det till exempel finns 10 programtyper men en sida bara passar de tre första programtyperna, eller om maxresultat är satt till 3, returneras tre. Om du vill komma åt resten av resultaten hämtar du efterföljande sidor med hjälp av den returnerade fortsättningstoken i nästa fråga. En tom fortsättningstoken returneras om det inte finns några efterföljande sidor.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-type-definition-kind-filter | Används för att filtrera på ApplicationTypeDefinitionKind som är den mekanism som används för att definiera en tjänst fabric-programtyp.  <br> - Standard - Standardvärde, som utför samma funktion som att välja "Alla". Värdet är 0.  <br> - Alla - Filter som matchar indata med alla ApplicationTypeDefinitionKind-värden. Värdet är 65535.  <br> - ServiceFabricApplicationPackage - Filter som matchar indata med ApplicationTypeDefinitionKind värde ServiceFabricApplicationPackage. Värdet är 1.  <br> - Komponera - Filter som matchar indata med ApplicationTypeDefinitionKind värde Komponera. Värdet är 2. |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --exclude-application-parameters --exclude-application-parameters --exclude-application-parameters -- | Flaggan som anger om programparametrar ska uteslutas från resultatet. |
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

## <a name="sfctl-application-unprovision"></a>sfctl ansökan avetablerande
Tar bort eller avregistrerar en tjänst fabric-programtyp från klustret.

Den här åtgärden kan bara utföras om alla programinstanser av programtypen har tagits bort. När programtypen är oregistrerad kan inga nya programinstanser skapas för den här programtypen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-type-name [Obligatoriskt] | Namnet på programtypen. |
| --application-type-version [Obligatoriskt] | Den version av programtypen som definieras i programmanifestet. |
| --async-parameter | Flaggan som anger om avetablen ska ske asynkront. När den är inställd på true returneras avetablen när begäran accepteras av systemet och avetablera åtgärden fortsätter utan någon tidsgräns. Standardvärdet är false. Vi rekommenderar dock att du ställer in den på true för stora programpaket som har etablerats. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-upgrade"></a>uppgradering av sfctl-program
Startar uppgradering av ett program i Service Fabric-klustret.

Validerar de angivna programuppgraderingsparametrarna och börjar uppgradera programmet om parametrarna är giltiga. Observera att uppgraderingsbeskrivningen ersätter den befintliga programbeskrivningen. Det innebär att om parametrarna inte anges skrivs de befintliga parametrarna i programmen över med listan över tomma parametrar. Detta skulle resultera i att programmet använder standardvärdet för parametrarna från programmanifestet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. <br><br> Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --application-version [Obligatoriskt] | Målprogramtypens version (finns i programmanifestet) för programuppgraderingen. |
| --parametrar [Obligatoriskt] | En JSON-kodad lista över programparameter åsidosättningar som ska tillämpas vid uppgradering av programmet. |
| --default-service-health-policy | JSON kodade specifikationen av hälsoprincipen som används som standard för att utvärdera hälsotillståndet för en tjänsttyp. |
| --fel-åtgärd | Åtgärden som ska utföras när en övervakad uppgradering stöter på övervakningsprincip eller hälsoprincipöverträdelser. |
| --force-restart | Starta om processer under uppgraderingen med kraft även när kodversionen inte har ändrats. |
| --health-check-retry-timeout | Hur lång tid det går mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfritt.  Standard\: PT0H10M0S. |
| --hälsokontroll-stabil varaktighet | Den tid som programmet eller klustret måste förbli felfritt innan uppgraderingen fortsätter till nästa uppgraderingsdomän.  Standard\: PT0H2M0S. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälsa-check-vänta-varaktighet | Hur lång tid du ska vänta efter att ha slutfört en uppgraderingsdomän innan hälsokontrollprocessen startas.  Standard\: 0. |
| --max-ohälsosam-appar | Den högsta tillåtna procentandelen av felaktiga distribuerade program. Representerad som ett tal mellan 0 och 100. |
| --läge | Det läge som används för att övervaka hälsotillståndet under en rullande uppgradering.  Standard\: unmonitoredauto. |
| --replica-set-check-timeout | Den maximala tiden för att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns oväntade problem. Mätt i sekunder. |
| --service-health-policy | JSON kodade kartan med hälsoprincip för tjänsttyp per tjänsttypsnamn. Kartan är tom som standard. |
| --timeout -t | Standard\: 60. |
| --upgrade-domain-timeout --upgrade-domain-timeout --upgrade-domain-timeout -- | Den tid som varje uppgraderingsdomän måste slutföra innan FailureAction körs.  Standard\: P10675199DT02H48M05.4775807S. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --upgrade-timeout --upgrade-timeout --upgrade-timeout -- | Hur lång tid den totala uppgraderingen måste slutföras innan FailureAction körs.  Standard\: P10675199DT02H48M05.4775807S. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --varning-som-fel | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl ansökan uppgradering-återuppta
Återupptar uppgraderingen av ett program i service fabric-klustret.

Återupptar en oövervakad manuell service fabric-programuppgradering. Service Fabric uppgraderar en uppgraderingsdomän i taget. För oövervakade manuella uppgraderingar väntar du på att du ska anropa det här API:et när du har slutfört en uppgraderingsdomän när Service Fabric har slutfört en uppgraderingsdomän.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --upgrade-domain-name [Obligatoriskt] | Namnet på uppgraderingsdomänen där uppgraderingen ska återupptas. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl program uppgradering-rollback
Startar att återställa den pågående uppgraderingen av ett program i service fabric-klustret.

Startar att återställa den aktuella programuppgraderingen till den tidigare versionen. Det här API:et kan bara användas för att återställa den aktuella pågående uppgraderingen som rullar framåt till ny version. Om programmet för närvarande inte uppgraderas använd StartApplicationUpgrade API för att uppgradera det till önskad version, inklusive att återställa till en tidigare version.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-upgrade-status"></a>uppgraderingsstatus för sfctl-program
Hämtar information om den senaste uppgraderingen som utförts för det här programmet.

Returnerar information om tillståndet för den senaste programuppgraderingen tillsammans med information för att underlätta felsökning av programhälsoproblem.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-application-upload"></a>sfctl ansökan ladda upp
Kopiera ett Programpaket för Service Fabric till bildarkivet.

Du kan också visa överföringsstatus för varje fil i paketet. Överföringsstatus skickas `stderr`till .

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --sökväg [Obligatoriskt] | Sökväg till lokalt programpaket. |
| --kompress | Gäller endast för Service Fabric-programpaket. Skapa en ny mapp som innehåller det komprimerade programpaketet till antingen standardplatsen eller till den plats som anges av parametern komprimerad plats och ladda sedan upp den nyskapade mappen. <br><br> Om det redan finns en komprimerad fil som genereras av sfctl skrivs den över om den här flaggan är inställd. Ett fel returneras om katalogen inte är ett programpaket. Om det redan är ett komprimerat programpaket kopieras mappen som den är. Som standard tas det nyligen skapade komprimerade programpaketet bort efter en lyckad överföring. Om uppladdningen inte lyckas, vänligen rensa manuellt det komprimerade paketet efter behov. Borttagningen tar inte bort några tomma dirs som kan ha skapats om parametern komprimerad plats refererar till icke-existerande kataloger. |
| --komprimerad plats | Platsen för att placera det komprimerade programpaketet. <br><br> Om ingen plats anges placeras det komprimerade paketet under en nyskapad mapp som heter sfctl_compressed_temp under den överordnade katalog som anges i sökvägsargumentet. Om sökvägsargumentet\:till exempel har värdet C /FolderA/AppPkg\:läggs det komprimerade paketet till c/folderA/sfctl_compressed_temp/apppkg. |
| --imagestore-string | Målbildsarkivet för att ladda upp programpaketet till.  Standardtyg\: \:ImageStore. <br><br> Om du vill överföra till en filplats startar du den här parametern med "file".\: Annars ska värdet vara anslutningssträngen för bildarkiv, till exempel standardvärdet. |
| --keep-komprimerad | Om det genererade komprimerade paketet ska behållas eller inte när det har slutförts. <br><br> Om inte inställt, sedan efter framgångsrikt slutförande, kommer de komprimerade apppaketen att tas bort. Om överföringen inte lyckades kommer programpaketet alltid att lagras i utdatakatalogen för återuppladdning. |
| --show-progress --show-progress --show-progress -- | Visa förlopp för filöverföring för stora paket. |
| --timeout -t | Den totala timeouten i sekunder. Överföringen misslyckas och returnerar fel när tidsgränsen för överföringen har passerats. Den här timeouten gäller för hela programpaketet och enskilda filtidsutgångar är lika med den återstående timeout-varaktigheten. Timeout innehåller inte den tid som krävs för att komprimera programpaketet.  Standard\: 300. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).
