---
title: Azure Service Fabric CLI-sfctl-egenskap | Microsoft Docs
description: Beskriver egenskaps kommandona för Service Fabric CLI-sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 34e6fc0d4e6e0817f9312a6565a2dd5dd99fdab9
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035253"
---
# <a name="sfctl-property"></a>sfctl property
Lagra och fråga efter egenskaper under Service Fabric namn.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort den angivna Service Fabric egenskapen. |
| Hämta | Hämtar den angivna Service Fabric egenskapen. |
| list | Hämtar information om alla Service Fabric egenskaper under ett givet namn. |
| föras | Skapar eller uppdaterar en Service Fabric-egenskap. |

## <a name="sfctl-property-delete"></a>ta bort sfctl-egenskap
Tar bort den angivna Service Fabric egenskapen.

Tar bort den angivna Service Fabric-egenskapen under ett visst namn. En egenskap måste skapas innan den kan tas bort.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn-ID [obligatoriskt] | Service Fabric namn, utan URI-schemat '\:Fabric '. |
| --egenskap-namn [obligatoriskt] | Anger namnet på egenskapen som ska hämtas. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-property-get"></a>Hämta sfctl-egenskap
Hämtar den angivna Service Fabric egenskapen.

Hämtar den angivna Service Fabric-egenskapen under ett visst namn. Detta kommer alltid att returnera både värde och metadata.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn-ID [obligatoriskt] | Service Fabric namn, utan URI-schemat '\:Fabric '. |
| --egenskap-namn [obligatoriskt] | Anger namnet på egenskapen som ska hämtas. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-property-list"></a>egenskaps lista för sfctl
Hämtar information om alla Service Fabric egenskaper under ett givet namn.

Ett Service Fabric namn kan ha en eller flera namngivna egenskaper som lagrar anpassad information. Den här åtgärden hämtar information om dessa egenskaper i en lista med sidor. Informationen innehåller namn, värde och metadata om var och en av egenskaperna.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn-ID [obligatoriskt] | Service Fabric namn, utan URI-schemat '\:Fabric '. |
| --fortsättnings-token | Parametern för fortsatt token används för att hämta nästa uppsättning resultat. En fortsättnings-token med ett värde som inte är tom inkluderas i svaret på API: et när resultatet från systemet inte passar i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller inte fortsättnings-token ett värde. Värdet för den här parametern får inte vara URL-kodat. |
| --Inkludera-värden | Tillåter att du anger om värdena för de egenskaper som returneras ska inkluderas. Sant om värden ska returneras med metadata. False för att returnera endast egenskaps-metadata. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-property-put"></a>sfctl egenskaps placering
Skapar eller uppdaterar en Service Fabric-egenskap.

Skapar eller uppdaterar den angivna Service Fabric-egenskapen under ett visst namn.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn-ID [obligatoriskt] | Service Fabric namn, utan URI-schemat '\:Fabric '. |
| --egenskap-namn [obligatoriskt] | Namnet på egenskapen Service Fabric. |
| --värde [obligatoriskt] | Beskriver ett egenskaps värde för Service Fabric. Detta är en JSON-sträng. <br><br> JSON-strängen har två fält, "typ" av data och "värde" för data. Värdet för "kind" måste vara det första objektet som ska visas i JSON-strängen och kan vara värdena Binary, Int64, Double, String eller GUID. Värdet ska vara seriellt-kan användas för de typer som anges. Värdena "kind" och "data" ska anges som strängar. |
| --anpassad-ID-typ | Egenskapens anpassade typ-ID. Med den här egenskapen kan användaren tagga typen för egenskapens värde. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).