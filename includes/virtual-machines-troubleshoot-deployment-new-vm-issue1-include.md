---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187341"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problem: anpassad avbildning; etablerings fel
Etablerings fel uppstår om du överför eller fångar en generaliserad avbildning av virtuella datorer som en specialiserad VM-avbildning eller vice versa. Den tidigare orsakar ett tids gräns fel för etableringen och den senare kommer att orsaka ett etablerings fel. Om du vill distribuera en anpassad avbildning utan fel måste du se till att bildens typ inte ändras under insamlings processen.

I följande tabell visas möjliga kombinationer av generaliserade och specialiserade avbildningar, vilken typ av fel som du kommer att stöta på och vad du behöver göra för att åtgärda felen.

