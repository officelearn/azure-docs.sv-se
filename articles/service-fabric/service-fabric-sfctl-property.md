---
title: Azure Service Fabric CLI- sfctl-egenskap
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för lagring och frågor.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905824"
---
# <a name="sfctl-property"></a>sfctl property
Lagra och frågeegenskaper under Service Fabric-namn.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort den angivna egenskapen Service Fabric. |
| get | Hämtar egenskapen Service Fabric. |
| lista | Hämtar information om alla Service Fabric-egenskaper under ett förnamn. |
| Sätta | Skapar eller uppdaterar en service fabric-egenskap. |

## <a name="sfctl-property-delete"></a>sfctl-egenskapen ta bort
Tar bort den angivna egenskapen Service Fabric.

Tar bort den angivna egenskapen Service Fabric under ett förnamn. En egenskap måste skapas innan den kan tas bort.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name-id [Obligatoriskt] | Namnet Service Fabric, utan\:URI-schemat för tyg. |
| --egenskap-name [Obligatoriskt] | Anger namnet på egenskapen som ska hämtas. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-property-get"></a>sfctl egendom få
Hämtar egenskapen Service Fabric.

Hämtar egenskapen Service Fabric under ett förnamn. Detta returnerar alltid både värde och metadata.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name-id [Obligatoriskt] | Namnet Service Fabric, utan\:URI-schemat för tyg. |
| --egenskap-name [Obligatoriskt] | Anger namnet på egenskapen som ska hämtas. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-property-list"></a>sfctl fastighetslista
Hämtar information om alla Service Fabric-egenskaper under ett förnamn.

Ett Service Fabric-namn kan ha en eller flera namngivna egenskaper som lagrar anpassad information. Den här åtgärden hämtar information om dessa egenskaper i en växlingslista. Informationen innehåller namn, värde och metadata om var och en av egenskaperna.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name-id [Obligatoriskt] | Namnet Service Fabric, utan\:URI-schemat för tyg. |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --include-värden | Gör det möjligt att ange om värdena för de returnerade egenskaperna ska inkluderas. Sant om värden ska returneras med metadata. Falskt att returnera endast egenskapsmetadata. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-property-put"></a>sfctl egendom sätta
Skapar eller uppdaterar en service fabric-egenskap.

Skapar eller uppdaterar egenskapen Angiven Service Fabric under ett förnamn.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name-id [Obligatoriskt] | Namnet Service Fabric, utan\:URI-schemat för tyg. |
| --egenskap-name [Obligatoriskt] | Namnet på egenskapen Service Fabric. |
| --värde [Obligatoriskt] | Beskriver egenskapsvärdet För Service Fabric. Det här är en JSON-sträng. <br><br> Json-strängen har två fält, datans "typ" och värdet, som anges som Data för data. Värdet "Typ" måste vara det första objektet som visas i JSON-strängen och kan vara värdena "Binär", "Int64", "Double", "String" eller "Guid". Värdet ska serialiseras till de angivna typerna. Både värdena "Sort" och "Data" ska anges som strängar. |
| --custom-id-typ | Egenskapens anpassade typ-ID. Med den här egenskapen kan användaren tagga typen av värdet för egenskapen. |
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