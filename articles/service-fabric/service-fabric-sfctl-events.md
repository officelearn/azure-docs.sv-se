---
title: Azure Service Fabric CLI- sfctl-händelser
description: Beskriver kommandona Service Fabric CLI sfctl-händelser.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906104"
---
# <a name="sfctl-events"></a>sfctl händelser
Hämta händelser från händelsearkivet (om EventStore-tjänsten redan är installerad).

EventStore-systemtjänsten kan läggas till genom en konfigurationsuppgradering till alla SFRP-kluster som körs >=6.4. Kontrollera följande webbadress\: \:https //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista över alla program | Hämtar alla programrelaterade händelser. |
| all-noder-lista | Hämtar alla noderrelaterade händelser. |
| all-partitioner-lista | Hämtar alla partitioner-relaterade händelser. |
| lista över alla tjänster | Hämtar alla tjänster-relaterade händelser. |
| programlista | Hämtar en programrelaterad händelse. |
| klusterlista | Hämtar alla klusterrelaterade händelser. |
| nodlista | Hämtar en nodrelaterade händelser. |
| partition-alla-repliker-lista | Hämtar alla replikrelaterade händelser för en partition. |
| partitionslista | Hämtar partitionsrelaterade händelser. |
| partition-replik-lista | Hämtar en partitionsreplikrelaterade händelser. |
| servicelista | Hämtar en tjänstrelaterad händelse. |

## <a name="sfctl-events-all-applications-list"></a>sfctl händelser alla program-lista
Hämtar alla programrelaterade händelser.

Svaret är en lista över ApplicationEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl händelser all-noder-lista
Hämtar alla noderrelaterade händelser.

Svaret är en lista över NodeEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl-händelser allpartitioner-lista
Hämtar alla partitioner-relaterade händelser.

Svaret är en lista över PartitionEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-all-services-list"></a>sfctl-evenemangslista för alla tjänster
Hämtar alla tjänster-relaterade händelser.

Svaret är en lista över ServiceEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-application-list"></a>sfctl händelser ansökan-lista
Hämtar en programrelaterad händelse.

Svaret är en lista över ApplicationEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-cluster-list"></a>klusterlista för sfctl-händelser
Hämtar alla klusterrelaterade händelser.

Svaret är en lista över ClusterEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-node-list"></a>sfctl-händelsenod-lista
Hämtar en nodrelaterade händelser.

Svaret är en lista över NodeEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl-händelser partition-alla-repliker-lista
Hämtar alla replikrelaterade händelser för en partition.

Svaret är en lista över ReplicaEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-partition-list"></a>partitionslista för sfctl-händelser
Hämtar partitionsrelaterade händelser.

Svaret är en lista över PartitionEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl-händelser partition-replik-lista
Hämtar en partitionsreplikrelaterade händelser.

Svaret är en lista över ReplicaEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --replica-id [Obligatoriskt] | Identifieraren för repliken. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-events-service-list"></a>tjänstlista för sfctl-händelser
Hämtar en tjänstrelaterad händelse.

Svaret är en lista över ServiceEvent-objekt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --end-time-utc [Krävs] | Sluttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --start-time-utc [Obligatoriskt] | Starttiden för en uppslagsfråga i ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Det här är en kommaavgränsad sträng som anger de typer av FabricEvents som endast ska inkluderas i svaret. |
| --exkludera-analys-händelser | Den här param inaktiverar hämtning av AnalysisEvents om sant skickas. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup -- | Den här param inaktiverar sökningen av CorrelatedEvents information om sant skickas. Annars bearbetas fältet CorrelationEvents och fältet HasCorrelatedEvents i varje FabricEvent. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

