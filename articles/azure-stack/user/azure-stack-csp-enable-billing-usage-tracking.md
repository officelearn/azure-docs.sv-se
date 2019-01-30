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
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 5d7398853e20702aef450e2532784f0dca7aac57
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246610"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Aktivera en Molntjänstleverantör att hantera din prenumeration för Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Om du använder Azure Stack med en Cloud Service Provider (CSP), kan du hantera din egen prenumeration för att komma åt resurser i Azure och i Azure Stack. Du kan också låta providern hantera din prenumeration åt dig. Den här artikeln visar hur du:

* Ge din service provider åtkomst till din prenumeration.
* Kontrollera att tjänstleverantören kan hantera din tjänst.

> [!NOTE]
> Om CSP: N inte hanterar ditt konto och du hoppar över följande steg, kan inte CSP: N hantera din Azure Stack-prenumeration åt dig.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Hantera din prenumeration med en Molntjänstleverantör

Lägg till CSP: N som **användaren** till din prenumeration.

1. Lägg till din CSP som gästanvändare med rollen till din klientkatalog. Anvisningar om hur du lägger till en användare finns i [lägga till nya användare till Azure Active Directory](../../active-directory/add-users-azure-active-directory.md)
2. CSP: N skapar lokala Azure Stack-prenumerationen för dig. Du är redo att börja använda Azure Stack.
3. Din CSP ska skapa en resurs i din prenumeration för att kontrollera att de kan även hantera dina resurser. De kan till exempel [skapa en Windows-dator med Azure Stack portal](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Aktivera Molntjänstleverantören att hantera din prenumeration med hjälp av RBAC-behörighet

Lägg till CSP: N som **ägare** till din prenumeration.

1. Lägg till din CSP som gästanvändare till din klientkatalog. Anvisningar om hur du lägger till en användare finns i [lägga till nya användare till Azure Active Directory](../../active-directory/add-users-azure-active-directory.md)
2. Lägg till den **ägare** rollen till gästanvändaren CSP. Anvisningar om hur du lägger till CSP-användaren till din prenumeration finns i [Use Role-Based Access Control för att hantera åtkomst till din Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md). CSP: N skapar lokala Azure Stack-prenumerationen för dig. Du är redo att börja använda Azure Stack.
3. Din CSP ska skapa en resurs i din prenumeration för att kontrollera att de kan hantera dina resurser.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du hämtar information om användning från Azure Stack i [användning och fakturering i Azure Stack](../azure-stack-billing-and-chargeback.md).
