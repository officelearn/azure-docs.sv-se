---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 0a4faad8bfe92a5389e0ee440aa3ccc404535955
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118091"
---
Gå till Azure Portal och [skapa en ny formulär igenkännings resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer). I fönstret **skapa** anger du följande information:

|    |    |
|--|--|
| **Namn** | Ett beskrivande namn för din resurs. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyNameFormRecognizer*. |
| **Prenumeration** | Välj den Azure-prenumeration som har beviljats åtkomst. |
| **Plats** | Platsen för din kognitiva tjänst instans. Olika platser kan orsaka svars tid, men har ingen inverkan på resursens tillgänglighet för körning. |
| **prisnivå** | Kostnaden för din resurs beror på vilken pris nivå du väljer och din användning. Mer information finns i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/)för API.
| **Resursgrupp** | Den [Azure-resurs grupp](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) som ska innehålla din resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

> [!IMPORTANT]
> Normalt när du skapar en kognitiv tjänst resurs i Azure Portal har du möjlighet att skapa en prenumerations nyckel för flera tjänster (används i flera kognitiva tjänster) eller en prenumerations nyckel för enskild tjänst (används endast med en viss kognitiv tjänst). Men eftersom formulär tolken är en för hands version ingår den inte i multi-service-prenumerationen och du kan inte skapa en tjänst prenumeration om du inte använder länken i Välkommen e-postmeddelandet.

När du har slutfört distributionen av formulärets tolknings resurs söker du efter och väljer den från listan **alla resurser** i portalen. Välj sedan fliken **snabb start** för att visa dina prenumerations data. Spara värdena för **KEY1** och **slut punkten** på en tillfällig plats. Du ska använda dem i följande steg.