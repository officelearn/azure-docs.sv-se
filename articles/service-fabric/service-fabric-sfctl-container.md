---
title: Azure Service Fabric CLI - sfctl behållare | Microsoft Docs
description: Beskriver kommandona Service Fabric CLI sfctl behållare.
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
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764037"
---
# <a name="sfctl-container"></a>sfctl behållare
Kör behållaren-relaterade kommandon på en klusternod.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| anropa api | Anropa behållaren REST API. |
| loggar | Hämta loggar för behållaren. |

## <a name="sfctl-container-invoke-api"></a>anropa sfctl-behållaren-api
Anropa behållaren REST API.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Tillämpningsprogrammets identitet. |
| --kod-paketet-instans-id [krävs] | Koden paketet instans-ID som kan hämtas av 'service code-paketet-list'. |
| ---paketet-kodnamnet [krävs] | Koden paketnamn. |
| --behållarens-api-uri-sökväg [krävs] | Behållaren REST API-URI-sökväg, Använd {ID} i stället för behållarens namn/id. |
| --nodnamn [krävs] | Namnet på noden. |
| ---manifest-tjänstnamn [krävs] | Manifestet tjänstnamn. |
| ---api-behållaren | HTTP-begärandetexten för behållaren REST API. |
| --behållaren-api-content-type | Content-type för behållaren REST-API som standard application/json. |
| --behållaren-api-http-verb | HTTP-verbet i behållaren REST-API som standard GET. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-container-logs"></a>sfctl behållaren loggar
Hämta loggar för behållaren.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Tillämpningsprogrammets identitet. |
| --kod-paketet-instans-id [krävs] | Koden paketet instans-ID som kan hämtas av 'service code-paketet-list'. |
| ---paketet-kodnamnet [krävs] | Koden paketnamn. |
| --nodnamn [krävs] | Namnet på noden. |
| ---manifest-tjänstnamn [krävs] | Manifestet tjänstnamn. |
| --pilslut | Bara returnera loggen rader i från slutet av loggarna. Ange som ett heltal eller alla Visa alla rader i loggen. Som standard för alla. |
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