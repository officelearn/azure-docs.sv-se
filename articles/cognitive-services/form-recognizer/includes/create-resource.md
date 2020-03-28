---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205837"
---
Gå <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="till Azure-portalen och skapa" target="_blank">en ny formulärkonformeringsresurs skapa en ny formkonformeringsresurs <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Ange följande information i fönstret **Skapa:**

|    |    |
|--|--|
| **Namn** | Ett beskrivande namn för din resurs. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyNameFormRecognizer*. |
| **Prenumeration** | Välj den Azure-prenumeration som har beviljats åtkomst. |
| **Location** | Platsen för din kognitiva tjänstinstans. Olika platser kan införa svarstid, men har ingen inverkan på resursens körningstillgänglighet. |
| **Prisnivå** | Kostnaden för din resurs beror på vilken prisnivå du väljer och din användning. Mer information finns i [API-prisinformationen](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Resursgrupp** | [Azure-resursgruppen](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) som ska innehålla din resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

> [!IMPORTANT]
> Normalt när du skapar en Cognitive Service-resurs i Azure-portalen har du möjlighet att skapa en prenumerationsnyckel för flera tjänster (som används för flera kognitiva tjänster) eller en prenumerationsnyckel för en tjänst (används endast med en viss kognitiv tjänst). Eftersom Formulär recognizer är en förhandsversion ingår den inte i prenumerationen med flera tjänster och du kan inte skapa prenumerationen på en enda tjänst om du inte använder länken i välkomstmeddelandet.

När formulärets identifierare är klar med distributionen hittar och väljer du den i listan **Alla resurser** i portalen. Välj sedan fliken **Snabbstart** för att visa dina prenumerationsdata. Spara värdena för **Key1** och **Slutpunkten** på en tillfällig plats. Du ska använda dem i följande steg.
