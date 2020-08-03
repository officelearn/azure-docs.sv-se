---
author: baanders
description: inkludera fil för roll krav i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407577"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Krav: behörighets krav

För att kunna slutföra alla steg i den här artikeln måste du klassificeras som en ägare i din Azure-prenumeration. 

Du kan kontrol lera behörighets nivån genom att köra det här kommandot i Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Om du är ägare `roleDefinitionName` är värdet i resultatet *ägare*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell fönster som visar utdata från kommandot AZ Role Assignment List":::

Om du upptäcker att värdet är *deltagare* eller något annat än *ägare*kan du fortsätta på något av följande sätt:
* Kontakta din prenumerations ägare och be om ägaren att slutföra stegen i den här artikeln för din räkning
* Kontakta antingen prenumerations ägaren eller någon med rollen administratör för användar åtkomst i prenumerationen och begär att du får behörighet att gå vidare till ägaren av prenumerationen. Huruvida detta är lämpligt beror på din organisation och din roll i den.