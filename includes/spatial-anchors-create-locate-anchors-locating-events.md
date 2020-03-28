---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "71180077"
---
När din watcher har `AnchorLocated` skapats kommer händelsen att avfyras för varje ankar som begärs. Den här händelsen utlöses när ett ankare finns eller om ankaret inte kan lokaliseras. Om denna situation inträffar, kommer orsaken anges i status. När alla ankare för en watcher bearbetas, hittas `LocateAnchorsCompleted` eller inte hittas, då händelsen kommer brand. Det finns en gräns på 35 identifierare per watcher. 
