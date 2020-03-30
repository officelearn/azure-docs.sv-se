---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187341"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problem: Anpassad bild; etableringsfel
Etableringsfel uppstår om du överför eller tar en generaliserad VM-avbildning som en specialiserad VM-avbildning eller vice versa. Den förstnämnda kommer att orsaka ett etableringstidsfel och det senare kommer att orsaka ett etableringsfel. Om du vill distribuera den anpassade avbildningen utan fel måste du se till att typen av avbildning inte ändras under insamlingsprocessen.

I följande tabell visas möjliga kombinationer av generaliserade och specialiserade bilder, feltypen du kommer att stöta på och vad du behöver göra för att åtgärda felen.

