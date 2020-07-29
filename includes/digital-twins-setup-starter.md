---
author: baanders
description: inkludera fil för start information i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: dcc616dd8d30bfadb751df9ba24d71d72675428a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371470"
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

Om du upptäcker att värdet är *deltagare* eller något annat än *ägare*kan du fortsätta på något av följande sätt:
* Kontakta din prenumerations ägare och be om ägaren att slutföra stegen i den här artikeln för din räkning
* Kontakta antingen prenumerations ägaren eller någon med rollen administratör för användar åtkomst i prenumerationen och begär att du får behörighet att gå vidare till ägaren av prenumerationen. Huruvida detta är lämpligt beror på din organisation och din roll i den.
