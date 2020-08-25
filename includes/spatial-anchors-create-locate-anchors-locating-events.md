---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "71180077"
---
När din bevakare har skapats `AnchorLocated` utlöses händelsen för varje fäst punkt som begärs. Den här händelsen utlöses när ett ankare finns eller om ankaret inte kan hittas. Om den här situationen inträffar anges orsaken i status. När alla ankare för en bevakare har bearbetats, hittats eller inte hittas, `LocateAnchorsCompleted` kommer händelsen att utlösas. Det finns en gräns på 35 identifierare per övervakare. 
