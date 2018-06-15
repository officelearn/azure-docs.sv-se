---
title: Aktivera en Molntjänstleverantör att hantera din prenumeration på Azure-stacken | Microsoft Docs
description: Aktivera tjänstleverantör att få åtkomst till en prenumeration i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357851"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Aktivera en Molntjänstleverantör att hantera din prenumeration på Azure-stacken

*Gäller för: Azure Stack integrerat system*

Om du använder Azure-stacken med en molntjänst-providers (CSP), kan du hantera din egen prenumeration för att komma åt resurser i Azure och Azure-stacken. Du kan också låta providern hantera din prenumeration för dig. Den här artikeln beskrivs hur du vill:

 * Ge service-providerns åtkomst till din prenumeration.
 * Kontrollera att tjänstleverantören kan hantera din tjänst.

> [!Note]
>  Om CSP: N inte hantera kontot, och du hoppar över följande steg, kan inte CSP: N hantera prenumerationen Azure Stack för dig.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Hantera din prenumeration med en Molntjänstleverantör

Lägg till CSP: N som **användaren** till din prenumeration.

1. Lägga till din CSP som gästanvändare med användarrollen i klientkatalogen.  Stegvisa instruktioner för att lägga till en användare finns [lägga till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP skapar lokala Azure Stack-prenumerationen för dig.
3. Du är redo att börja använda Azure-stacken.
4. Din CSP ska skapa en resurs i din prenumeration för att verifiera att de kan även hantera dina resurser. De kan till exempel [skapa en virtuell Windows-dator med Azure Stack-portalen](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Aktivera Molntjänstleverantören att hantera din prenumeration med RBAC rättigheter

Lägg till CSP: N som **ägare** till din prenumeration.

1. Lägga till din CSP som gästanvändare i klientkatalogen.  Stegvisa instruktioner för att lägga till en användare finns [lägga till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Lägg till rollen som ägare till gästanvändaren CSP. Stegvisa instruktioner för att lägga till CSP-användare i din prenumeration finns [Use Role-Based behörighet att hantera åtkomst till resurserna i Azure-prenumeration](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP skapar lokala Azure Stack-prenumerationen för dig.
4. Du är redo att börja använda Azure-stacken.
5. Din CSP ska skapa en resurs i din prenumeration för att verifiera att de kan hantera dina resurser.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hämtar information om användning från Azure-stacken i [användnings- och fakturering i Azure-stacken](../azure-stack-billing-and-chargeback.md).
