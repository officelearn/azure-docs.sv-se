---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993133"
---
När din bevakare har skapats `AnchorLocated` utlöses händelsen för varje fäst punkt som begärs. Den här händelsen utlöses när ett ankare finns eller om ankaret inte kan hittas. Om den här situationen inträffar anges orsaken i status. När alla ankare för en bevakare har bearbetats, hittats eller inte hittas, `LocateAnchorsCompleted` kommer händelsen att utlösas. Det finns en gräns på 35 identifierare per övervakare. 
