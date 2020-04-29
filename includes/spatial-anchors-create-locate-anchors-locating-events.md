---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "71180077"
---
När din bevakare har skapats utlöses `AnchorLocated` händelsen för varje fäst punkt som begärs. Den här händelsen utlöses när ett ankare finns eller om ankaret inte kan hittas. Om den här situationen inträffar anges orsaken i status. När alla ankare för en bevakare har bearbetats, hittats eller inte hittas, `LocateAnchorsCompleted` kommer händelsen att utlösas. Det finns en gräns på 35 identifierare per övervakare. 
