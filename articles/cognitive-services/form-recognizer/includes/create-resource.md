---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: c2d3119283b5b75eeca6b0e0737f62eee00e81c6
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237471"
---
När du har beviljats åtkomst till att använda formulär igenkänning får du ett välkomst meddelande med flera länkar och resurser. Använd länken "Azure Portal" i meddelandet för att öppna Azure Portal och skapa en formulär igenkännings resurs. I fönstret **skapa** anger du följande information:

|    |    |
|--|--|
| **Namn** | Ett beskrivande namn för din resurs. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyNameFormRecognizer*. |
| **Prenumeration** | Välj den Azure-prenumeration som har beviljats åtkomst. |
| **Plats** | Platsen för din kognitiva tjänst instans. Olika platser kan orsaka svars tid, men har ingen inverkan på resursens tillgänglighet för körning. |
| **prisnivå** | Kostnaden för din resurs beror på vilken pris nivå du väljer och din användning. Mer information finns i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/)för API.
| **Resursgrupp** | Den [Azure-resurs grupp](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) som ska innehålla din resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

> [!IMPORTANT]
> Normalt när du skapar en kognitiv tjänst resurs i Azure Portal har du möjlighet att skapa en prenumerations nyckel för flera tjänster (används i flera kognitiva tjänster) eller en prenumerations nyckel för enskild tjänst (används endast med en viss kognitiv tjänst). Men eftersom formulär tolken är en för hands version ingår den inte i multi-service-prenumerationen och du kan inte skapa en tjänst prenumeration om du inte använder länken i Välkommen e-postmeddelandet.

När du har slutfört distributionen av formulärets tolknings resurs söker du efter och väljer den från listan **alla resurser** i portalen. Välj sedan fliken **nycklar** för att visa dina prenumerations nycklar. Antingen kommer nyckeln ge appen åtkomst till resursen. Kopiera värdet för **nyckel 1**.