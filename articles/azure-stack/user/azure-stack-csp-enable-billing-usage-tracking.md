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
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Aktivera en Molntjänstleverantör att hantera din prenumeration på Azure-stacken

*Gäller för: Azure Stack integrerat system*

Om du använder Azure-stacken med en molntjänst-providers (CSP), hanteras din åtkomst till resurser i din Azure-prenumeration och Azure-stacken av providern. Eller så kan du hantera din egen prenumeration. Den här artikeln beskrivs hur du kan aktivera tjänstleverantören för att få åtkomst till din prenumeration för din räkning och kontrollera att tjänstleverantören kan hantera din tjänst.

> [!Note]  
>  Om följande hoppas över och CSP: N inte redan hanterar ditt konto, CSP: N inte hantera din Azure Stack-prenumeration för din räkning.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Hantera din prenumeration med en Molntjänstleverantör

1. Lägga till din CSP som gästanvändare med användarrollen i klientkatalogen.  Stegvisa instruktioner för att lägga till en användare finns [lägga till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP skapar sedan lokala Azure Stack-prenumerationen för dig.
3. Du är redo att börja använda Azure-stacken.
3. Din CSP bör sedan skapa en resurs i din prenumeration för att verifiera att de kan även hantera dina resurser. De kan till exempel [skapa en virtuell Windows-dator med Azure Stack-portalen](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Aktivera Molntjänstleverantören att hantera din prenumeration med RBAC rättigheter

Lägga till CSP: N som ägare till prenumerationen. 

1. Lägg till din CSP som gästanvändare. med rollen som ägare till din klient-katalog.  Stegvisa instruktioner för att lägga till en användare finns [lägga till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Lägg till ägarrollen gästanvändaren CSP. Stegvisa instruktioner för att lägga till CSP-användare i din prenumeration finns [Use Role-Based behörighet att hantera åtkomst till resurserna i Azure-prenumeration](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP skapar sedan lokala Azure Stack-prenumerationen för dig.
4. Du är redo att börja använda Azure-stacken.
5. Din CSP bör sedan skapa en resurs i din prenumeration för att verifiera att de kan hantera dina resurser. 

## <a name="next-steps"></a>Nästa steg

  - Läs mer om hur du hämtar information om användning från Azure-stacken i [användnings- och fakturering i Azure-stacken](../azure-stack-billing-and-chargeback.md).
