---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123850"
---
## <a name="issue-custom-image-provisioning-errors"></a>Ärende: Anpassad avbildning; etablering fel
Etablering fel uppstå om du överför eller avbilda en generaliserad VM-avbildning som en specialiserad avbildning eller vice versa. Den tidigare versionen kommer att orsaka en etablering timeout-fel och det senare som kan orsaka etablering. Om du vill distribuera den anpassade avbildningen utan fel, måste du kontrollera att typ av avbildningen inte ändras under den här processen.

I följande tabell visas möjliga kombinationer av generaliserade och specialiserade bilder, feltypen inträffar och vad du behöver göra för att åtgärda felen.

<!--Update_Description: wording update, update link-->