---
title: Aktivera en Molntjänstleverantör att hantera din prenumeration för Azure Stack | Microsoft Docs
description: Aktivera tjänstleverantör för att få åtkomst till en prenumeration i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: fca08ef1d803c3bd47b0ae925c4dd12255175f2c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630723"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Aktivera en Molntjänstleverantör att hantera din prenumeration för Azure Stack

*Gäller för: integrerade Azure Stack-system*

Om du använder Azure Stack med en Cloud Service Provider (CSP), kan du hantera din egen prenumeration för att komma åt resurser i Azure och i Azure Stack. Du kan också låta providern hantera din prenumeration åt dig. Den här artikeln visar hur du:

 * Ge din service provider åtkomst till din prenumeration.
 * Kontrollera att tjänstleverantören kan hantera din tjänst.

> [!Note]
>  Om CSP: N är inte hanterar ditt konto och du hoppar över följande steg, kan inte CSP: N hantera din Azure Stack-prenumeration åt dig.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Hantera din prenumeration med en Molntjänstleverantör

Lägg till CSP: N som **användaren** till din prenumeration.

1. Lägg till din CSP som gästanvändare med rollen till din klientkatalog.  Stegvisa instruktioner för att lägga till en användare finns [lägga till nya användare till Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP: N skapar lokala Azure Stack-prenumerationen för dig.
3. Du är redo att börja använda Azure Stack.
4. Din CSP ska skapa en resurs i din prenumeration för att kontrollera att de kan även hantera dina resurser. De kan till exempel [skapa en Windows-dator med Azure Stack portal](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Aktivera Molntjänstleverantören att hantera din prenumeration med hjälp av RBAC-behörighet

Lägg till CSP: N som **ägare** till din prenumeration.

1. Lägg till din CSP som gästanvändare till din klientkatalog.  Stegvisa instruktioner för att lägga till en användare finns [lägga till nya användare till Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Lägg till rollen ägare till gästanvändaren CSP. Stegvisa instruktioner för att lägga till CSP-användare i din prenumeration finns [Use Role-Based Access Control för att hantera åtkomst till din Azure-prenumerationsresurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP: N skapar lokala Azure Stack-prenumerationen för dig.
4. Du är redo att börja använda Azure Stack.
5. Din CSP ska skapa en resurs i din prenumeration för att kontrollera att de kan hantera dina resurser.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hämtar information om användning från Azure Stack i [användning och fakturering i Azure Stack](../azure-stack-billing-and-chargeback.md).
