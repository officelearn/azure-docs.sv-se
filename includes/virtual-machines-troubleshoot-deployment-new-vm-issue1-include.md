---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226672"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problem: Anpassad avbildning; etablering fel
Etablering fel uppstå om du överför eller avbilda en generaliserad VM-avbildning som en specialiserad avbildning eller vice versa. Den tidigare versionen kommer att orsaka en etablering timeout-fel och det senare som kan orsaka etablering. Om du vill distribuera den anpassade avbildningen utan fel, måste du kontrollera att typ av avbildningen inte ändras under den här processen.

I följande tabell visas möjliga kombinationer av generaliserade och specialiserade bilder, feltypen inträffar och vad du behöver göra för att åtgärda felen.

