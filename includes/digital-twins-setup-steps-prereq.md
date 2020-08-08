---
author: baanders
description: inkludera fil för steg översikt och behörighets krav i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009684"
---
>[!NOTE]
>Dessa åtgärder är avsedda att slutföras av en användare med möjlighet att hantera både resurser och användar åtkomst i Azure-prenumerationen. Även om vissa steg kan slutföras med lägre behörigheter, krävs samarbetet för någon med dessa behörigheter för att helt kunna konfigurera en användbar instans. Visa mer information om detta i avsnittet krav [*: nödvändiga behörigheter*](#prerequisites-permission-requirements) nedan.

En fullständig installation av en ny Azure Digitals dubblare-instans består av tre delar:
1. **Skapar instansen**
2. Konfigurera **användar åtkomst behörigheter**: Azure-användare måste ha rollen *Azure Digitals-ägare (för hands version)* på Azures digitala dubbla instansen för att kunna hantera den och dess data. I det här steget ska du som ägare/administratör av Azure-prenumerationen tilldela rollen till den person som ska hantera din Azure Digital-instansen. Detta kan vara själv eller någon annan i din organisation.
3. Konfigurera **åtkomst behörigheter för klient program**: det är vanligt att skriva ett klient program som ska användas för att interagera med din instans. För att klient programmet ska kunna komma åt Azures digitala dubbla, måste du konfigurera en *app-registrering* i [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) att klient programmet ska använda för att autentisera till instansen.

Du behöver en Azure-prenumeration för att kunna fortsätta. Du kan ställa in en kostnads fri [här](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites-permission-requirements"></a>Krav: behörighets krav

För att kunna slutföra alla steg i den här artikeln måste du ha en [roll i din prenumeration](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) som har följande behörigheter:
* Skapa och hantera Azure-resurser
* Hantera användar åtkomst till Azure-resurser (inklusive beviljande och delegering av behörigheter)

Vanliga roller som uppfyller detta krav är *ägare*, *konto administratör*eller kombinationen av *användar åtkomst administratör* och *deltagare*. En fullständig förklaring av roller och behörigheter, inklusive vilka behörigheter som ingår i andra roller, finns i [*klassiska prenumerations administratörs roller, Azure-roller och Azure AD-roller*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) i Azure RBAC-dokumentationen.

Om du vill visa din roll i din prenumeration går du till [sidan prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal (du kan använda den här länken eller leta efter *prenumerationer* med Portal Sök fältet). Leta efter namnet på den prenumeration som du använder och Visa rollen för den i kolumnen *min roll* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Vy av sidan prenumerationer i Azure Portal, som visar användare som ägare" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Om du upptäcker att värdet är *deltagare*eller en annan roll som inte har de *behörigheter som anges* ovan, kan du kontakta användaren på prenumerationen som har dessa behörigheter (till exempel en prenumerations ägare eller konto administratör) och fortsätta på något av följande sätt:
* Begär att de slutför stegen i den här artikeln för din räkning
* Begär att de höjer din roll i prenumerationen så att du får behörighet att fortsätta. Huruvida detta är lämpligt beror på din organisation och din roll i den.