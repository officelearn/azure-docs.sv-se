---
author: baanders
description: inkludera fil för start information i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 3f19674c0e25ebf5cddc2dfb45580a15d04fad46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099887"
---
>[!NOTE]
>Dessa åtgärder är avsedda att slutföras av en användare med en *ägar* roll i Azure-prenumerationen. Även om vissa delar kan slutföras utan denna utökade behörighet, krävs en ägares samarbete för att helt konfigurera en användbar instans. Visa mer information om detta i avsnittet krav [*: nödvändiga behörigheter*](#prerequisites-permission-requirements) nedan.

En fullständig installation av en ny Azure Digitals dubblare-instans består av tre delar:
1. **Skapar instansen**
2. Konfigurera **din användares åtkomst behörigheter**: din Azure-användare måste ha rollen *Azure Digitals-ägare (förhands granskning)* på instansen för att kunna utföra hanterings aktiviteter. I det här steget tilldelar du själv den här rollen (om du är ägare i Azure-prenumerationen) eller skaffa en ägare till din prenumeration för att tilldela den till dig.
3. Konfigurera **åtkomst behörigheter för klient program**: det är vanligt att skriva ett klient program som du använder för att interagera med din instans. För att klient programmet ska kunna komma åt Azures digitala dubbla, måste du konfigurera en *app-registrering* i [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) att klient programmet ska använda för att autentisera till instansen.

Du behöver en Azure-prenumeration för att kunna fortsätta. Du kan ställa in en kostnads fri [här](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Krav: behörighets krav

För att kunna slutföra alla steg i den här artikeln måste du klassificeras som en ägare i din Azure-prenumeration. 

Du kan kontrol lera behörighets nivån genom att köra det här kommandot i Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Om du är ägare `roleDefinitionName` är värdet i resultatet *ägare*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Cloud Shell fönster som visar utdata från kommandot AZ Role Assignment List":::

Om du upptäcker att värdet är *deltagare* eller något annat än *ägare*kan du kontakta din prenumerations ägare och fortsätta på något av följande sätt:
* Be om ägaren att slutföra stegen i den här artikeln för din räkning
* Be om ägaren att öka dig till ägare av prenumerationen även, så att du får behörighet att gå vidare. Huruvida detta är lämpligt beror på din organisation och din roll i den.
