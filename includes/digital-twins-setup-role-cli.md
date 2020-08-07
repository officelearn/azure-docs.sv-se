---
author: baanders
description: inkludera fil för roll krav i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832369"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Krav: behörighets krav

För att kunna slutföra alla steg i den här artikeln måste du klassificeras som en ägare i din Azure-prenumeration. 

Du kan kontrol lera behörighets nivån genom att köra det här kommandot i Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Om det här kommandot returnerar ett fel som anger att CLI **inte kan hitta användare eller tjänstens huvud namn i diagram databasen**:
>
> Använd användarens *objekt-ID* i stället för ditt e-postmeddelande för resten av den här artikeln. Detta kan inträffa för användare på personliga [Microsoft-konton (MSA: er)](https://account.microsoft.com/account). 
>
> Använd [Azure Portal sidan för Azure Active Directory användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) för att välja ditt användar konto och öppna dess information. Kopiera användarens *ObjectID*:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Visning av användar sida i Azure Portal som markerar GUID i fältet objekt-ID" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Upprepa sedan kommandot roll tilldelnings lista med hjälp av användarens *objekt-ID* i stället för din e-post.

När du har kört kommandot roll tilldelnings lista, om du är ägare, `roleDefinitionName` är värdet i resultatet *ägare*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell fönster som visar utdata från kommandot AZ Role Assignment List":::

Om du upptäcker att värdet är *deltagare* eller något annat än *ägare*kan du fortsätta på något av följande sätt:
* Kontakta din prenumerations ägare och be om ägaren att slutföra stegen i den här artikeln för din räkning
* Kontakta antingen prenumerations ägaren eller någon med rollen administratör för användar åtkomst i prenumerationen och begär att du får behörighet att gå vidare till ägaren av prenumerationen. Huruvida detta är lämpligt beror på din organisation och din roll i den.