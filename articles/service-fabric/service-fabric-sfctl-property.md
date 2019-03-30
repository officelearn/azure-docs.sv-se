---
title: Azure Service Fabric CLI - sfctl egenskapen | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl egenskapen kommandon.
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
ms.openlocfilehash: 54cb9f604e9d1b817947990e657390387df6c881
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664921"
---
# <a name="sfctl-property"></a>sfctl property
Egenskaper för Store och fråga under Service Fabric-namn.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort den angivna egenskapen för Service Fabric. |
| Hämta | Hämtar den angivna egenskapen för Service Fabric. |
| lista | Hämtar information om alla Service Fabric-egenskaper under ett givet namn. |
| Placera | Skapar eller uppdaterar en egenskap för Service Fabric. |

## <a name="sfctl-property-delete"></a>sfctl egenskapen delete
Tar bort den angivna egenskapen för Service Fabric.

Tar bort den angivna egenskapen för Service Fabric under ett givet namn. En egenskap måste skapas innan den kan tas bort.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – namn-id [krävs] | Service Fabric namnet, utan de ”fabric\:” URI-schema. |
| --property-name [Required] | Anger namnet på egenskapen som ska hämtas. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-property-get"></a>sfctl egenskapen get
Hämtar den angivna egenskapen för Service Fabric.

Hämtar den angivna egenskapen för Service Fabric under ett givet namn. Detta returnerar alltid både värde och metadata.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – namn-id [krävs] | Service Fabric namnet, utan de ”fabric\:” URI-schema. |
| --property-name [Required] | Anger namnet på egenskapen som ska hämtas. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-property-list"></a>sfctl egenskapslistan
Hämtar information om alla Service Fabric-egenskaper under ett givet namn.

Ett Service Fabric-namn kan ha en eller flera namngivna egenskaper som lagrar information om anpassade. Den här åtgärden hämtar information om de här egenskaperna i en växlade lista. Informationen omfattar namn, värde och metadata om var och en av egenskaperna.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – namn-id [krävs] | Service Fabric namnet, utan de ”fabric\:” URI-schema. |
| --continuation-token | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| – innehåller värden | Kan du ange om du vill inkludera värdena för de egenskaper som returneras. SANT om värden ska returneras med metadata; False för att returnera endast egenskapen metadata. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-property-put"></a>sfctl egenskapen put
Skapar eller uppdaterar en egenskap för Service Fabric.

Skapar eller uppdaterar den angivna egenskapen för Service Fabric under ett givet namn.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – namn-id [krävs] | Service Fabric namnet, utan de ”fabric\:” URI-schema. |
| --property-name [Required] | Namnet på egenskapen Service Fabric. |
| --värdet [krävs] | Beskriver ett egenskapsvärde för Service Fabric. Det här är en JSON-sträng. <br><br> Json-sträng har två fält, typ av data och värdet av data. ”Motsvarande”-värdet måste vara det första objektet ska visas i JSON-sträng och kan vara värdena 'Binary', 'Int64', 'Double', 'String' eller 'Guid'. Värdet ska kunna serialisera-de angivna typerna. Både ”motsvarande” och ”Data” värden måste anges som strängar. |
| --anpassad-id-typ | Egenskapens anpassad typ-id. Med den här egenskapen kan kan användaren tagga typ av värdet för egenskapen. |
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