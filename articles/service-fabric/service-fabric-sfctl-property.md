---
title: Azure Service Fabric CLI - sfctl egenskapen | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl egenskapen-kommandon.
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
ms.openlocfilehash: acade3d828c785af9468baa30086d3b79542f9b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764031"
---
# <a name="sfctl-property"></a>Egenskapen sfctl
Lagra och fråga egenskaper under Service Fabric-namn.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort den angivna Service Fabric-egenskapen. |
| Hämta | Hämtar den angivna Service Fabric-egenskapen. |
| lista | Hämtar information om alla Service Fabric-egenskaper under ett angivet namn. |
| Placera | Skapar eller uppdaterar en Service Fabric-egenskap. |

## <a name="sfctl-property-delete"></a>ta bort sfctl-egenskap
Tar bort den angivna Service Fabric-egenskapen.

Tar bort den angivna Service Fabric-egenskapen under ett angivet namn. En egenskap måste skapas innan den kan tas bort.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --namn-id [krävs] | Service Fabric namn utan den ' fabric\:-URI-schema. |
| --egenskapsnamn [krävs] | Anger namnet på egenskapen som ska hämtas. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-property-get"></a>Egenskapen get sfctl
Hämtar den angivna Service Fabric-egenskapen.

Hämtar den angivna Service Fabric-egenskapen under ett angivet namn. Detta returnerar alltid både värde och metadata.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --namn-id [krävs] | Service Fabric namn utan den ' fabric\:-URI-schema. |
| --egenskapsnamn [krävs] | Anger namnet på egenskapen som ska hämtas. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-property-list"></a>sfctl egenskapslistan
Hämtar information om alla Service Fabric-egenskaper under ett angivet namn.

Ett namn för Service Fabric kan ha en eller flera namngivna egenskaper som lagrar anpassad information. Den här åtgärden hämtar information om dessa egenskaper i en växlingsbar lista. Informationen innehåller namn, värde och metadata om var och en av egenskaperna.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --namn-id [krävs] | Service Fabric namn utan den ' fabric\:-URI-schema. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --innehåller värden | Kan du ange om du vill inkludera värdena för egenskaperna som returneras. TRUE om värden ska returneras med metadata; False för att returnera endast egenskapmetadata. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-property-put"></a>sfctl egenskapen put
Skapar eller uppdaterar en Service Fabric-egenskap.

Skapar eller uppdaterar den angivna Service Fabric-egenskapen under ett angivet namn.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --namn-id [krävs] | Service Fabric namn utan den ' fabric\:-URI-schema. |
| --egenskapsnamn [krävs] | Namnet på egenskapen Service Fabric. |
| --värdet [krävs] | Beskriver ett Service Fabric-värde för egenskapen. Det här är en JSON-sträng. <br><br> Json-strängen har två fält, typ av data och värdet av data. Värdet för ”typ” måste vara det första objektet visas i JSON-strängen och kan vara värden 'Binary', 'Int64', 'Double', 'String' eller 'Guid'. Värdet ska kunna serialisera-de angivna typerna. Både ”typ” och ”uppgifter” värden måste anges som strängar. |
| --anpassad-id-typ | Ange egenskapens anpassad-ID Med den här egenskapen kan kan användaren tagga typ av värdet på egenskapen. |
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