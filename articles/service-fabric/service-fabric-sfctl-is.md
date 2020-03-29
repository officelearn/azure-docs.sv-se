---
title: Azure Service Fabric CLI- sfctl är
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista över kommandon för hantering av infrastruktur.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906088"
---
# <a name="sfctl-is"></a>sfctl is
Fråga och skicka kommandon till infrastrukturtjänsten.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| command | Anropar ett administrativt kommando i den angivna infrastrukturtjänstinstansen. |
| DocumentDB | Anropar en skrivskyddad fråga på den angivna infrastrukturtjänstinstansen. |

## <a name="sfctl-is-command"></a>sfctl är kommando
Anropar ett administrativt kommando i den angivna infrastrukturtjänstinstansen.

För kluster som har en eller flera instanser av infrastrukturtjänsten konfigurerade, är det här API:et ett sätt att skicka infrastrukturspecifika kommandon till en viss instans av infrastrukturtjänsten. Tillgängliga kommandon och motsvarande svarsformat varierar beroende på vilken infrastruktur klustret körs på. Det här API:et stöder plattformen Service Fabric. Det är inte tänkt att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kommando [Obligatoriskt] | Texten i kommandot som ska anropas. Innehållet i kommandot är infrastrukturspecifikt. |
| --service-id | Infrastrukturtjänstens identitet. <br><br> Detta är det fullständiga namnet på infrastrukturtjänsten utan URI-systemet "fabric".\: Den här parametern krävs endast för klustret som har mer än en instans av infrastrukturtjänsten som körs. |
| --timeout -t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-is-query"></a>sfctl är fråga
Anropar en skrivskyddad fråga på den angivna infrastrukturtjänstinstansen.

För kluster som har en eller flera instanser av infrastrukturtjänsten konfigurerade, är det här API:et ett sätt att skicka infrastrukturspecifika frågor till en viss instans av infrastrukturtjänsten. Tillgängliga kommandon och motsvarande svarsformat varierar beroende på vilken infrastruktur klustret körs på. Det här API:et stöder plattformen Service Fabric. Det är inte tänkt att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kommando [Obligatoriskt] | Texten i kommandot som ska anropas. Innehållet i kommandot är infrastrukturspecifikt. |
| --service-id | Infrastrukturtjänstens identitet. <br><br> Detta är det fullständiga namnet på infrastrukturtjänsten utan URI-systemet "fabric".\: Den här parametern krävs endast för klustret som har mer än en instans av infrastrukturtjänsten som körs. |
| --timeout -t | Standard\: 60. |

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
