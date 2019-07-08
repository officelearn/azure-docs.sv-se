---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: a37087c90b6c9b3629402c7a8c2fa5861e46ae9a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592504"
---
När du har beviljats åtkomst till att använda formatet igenkännande, får du en Välkommen e-postmeddelande med flera länkar och resurser. Använda länken ”Azure-portalen” i meddelandet för att öppna Azure-portalen och skapa en resurs för formuläret Igenkännande. I den **skapa** fönstret, ange följande information:

|    |    |
|--|--|
| **Name** | Ett beskrivande namn för din resurs. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyNameFormRecognizer*. |
| **Prenumeration** | Välj den Azure-prenumeration som har beviljats åtkomst. |
| **Location** | Platsen för din cognitive service-instans. Olika platser kan introducera svarstid, men har ingen inverkan på runtime tillgängligheten för din resurs. |
| **prisnivå** | Kostnaden för din resurs beror på prisnivå som du väljer och din användning. Mer information finns i API: et [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Resursgrupp** | Den [Azure-resursgrupp](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) som innehåller din resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

> [!IMPORTANT]
> När du skapar en resurs för Cognitive Service i Azure-portalen har normalt möjlighet att skapa en flera tjänster prenumerationsnyckel (används för flera kognitiva tjänster) eller en prenumerationsnyckel för en tjänst (används endast med en viss cognitive service). Eftersom formulär Igenkännande är en förhandsversionen, ingår inte i flera tjänster prenumeration och du kan inte skapa en tjänst-prenumeration om du inte använder länken i välkomstmeddelandet.

När formuläret Igenkännande resursen har distribution, hitta och välja den från den **alla resurser** listan på portalen. Välj sedan den **nycklar** fliken för att visa dina prenumerationsnycklar. Antingen nyckeln får din appåtkomst till resursen. Kopiera värdet för **nyckel 1**. Du kan använda den i nästa avsnitt.