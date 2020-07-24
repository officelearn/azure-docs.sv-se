---
title: Hantera registrerings princip – Azure
description: I det här avsnittet beskrivs hur du hanterar registrerings principer.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011489"
---
# <a name="manage-recording-policy"></a>Hantera inspelningsprincip

Du kan använda video analys i real tid för IoT Edge för [kontinuerlig videoinspelning](continuous-video-recording-concept.md), där du kan spela in video i molnet för veckor eller månader. Du kan hantera längden (i dagar) för det moln arkivet genom att använda de [livs cykel hanterings verktyg](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) som är inbyggda i Azure Storage.  

Ditt medie tjänst konto är länkat till ett Azure Storage konto och när du spelar in video i molnet skrivs innehållet till en medie tjänst [till gång](../latest/assets-concept.md). Varje till gång mappas till en behållare i lagrings kontot. med livs cykel hantering kan du definiera en [princip](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) för ett lagrings konto, där du kan ange en [regel](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) som följande.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

Regeln ovan:

* Gäller för alla block-blobar i lagrings kontot.
* Anger att när blobar åldras efter 30 dagar flyttas de från frekvent [åtkomst nivå till](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)låg frekvent.
* Och när blobar åldras efter 90 dagar måste de tas bort.

Eftersom live video analys arkiverar din video i angivna tidsenheter kommer din till gång att innehålla en serie blobbar, en BLOB per segment. När livs cykel hanterings principen använder och tar bort äldre blobbar fortsätter du att kunna komma åt och spela upp återstående blobbar via media service-API: er. Mer information finns i [uppspelnings inspelningar](playback-recordings-how-to.md). 

## <a name="limitations"></a>Begränsningar

Nedan följer några kända begränsningar för livs cykel hantering:

* Du kan ha högst 100 regler i principen och varje regel kan ange upp till 10 behållare. Så om du vill ha olika registrerings principer (t. ex. tre dagars Arkiv för kameran som är i parkerings parti, 30 dagar för kameran i inläsnings dockan och 180 dagar för kameran bakom utcheckningen) och sedan ett media service-konto kan du anpassa reglerna för högst 1000 kameror.
* Princip uppdateringar för livs cykel hantering är inte omedelbart. Mer information finns i [avsnittet Vanliga frågor och svar](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) .
* Om du väljer att tillämpa en princip där blobbar flyttas till den låg frekventa nivån, kan uppspelningen av den delen av arkivet påverkas. Du kan se ytterligare fördröjningar eller sporadiska fel. Media Services stöder inte uppspelning av innehåll i Arkiv lag rings nivån.

## <a name="next-steps"></a>Nästa steg

[Uppspelning av inspelningar](playback-recordings-how-to.md)
