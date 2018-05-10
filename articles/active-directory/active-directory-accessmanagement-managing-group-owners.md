---
title: Nästa steg för hantering med hjälp av grupper | Microsoft Docs
description: Avancerade hur-för hantering av säkerhetsgrupper och hur du använder dessa grupper för att hantera åtkomst till en resurs.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: e4703ab5d9f4b8593e758764e50455672e368e18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="managing-owners-for-a-group"></a>Hantera ägare för en grupp
När en resursägare har tilldelats till en resurs till en Azure AD-grupp, hanteras medlemskap i gruppen av gruppägare. Effektivt resursägaren behörighet att tilldela användare till resursen till ägare av gruppen.

## <a name="add-an-owner-to-a-group"></a>Lägg till en ägare till en grupp

1. I den [administrationscentret för Azure AD](https://aad.portal.azure.com)väljer **användare och grupper**.
2. Välj **alla grupper**, och sedan öppna den grupp som du vill lägga till ägare till.
3. Välj **lägga till ägare**.
4. På den **lägga till ägare** markerar du den användare som du vill lägga till som ägare av den här gruppen och kontrollera att namnet läggs till i **valda** fönstret.

## <a name="remove-an-owner-from-a-group"></a>Tar bort en ägare från en grupp

1. I den [administrationscentret för Azure AD](https://aad.portal.azure.com)väljer **användare och grupper**.
2. Välj **alla grupper**, och sedan öppna den grupp som du vill ta bort ägare.
3. Välj den **ägare** fliken.
4. Välj en ägare som du vill ta bort från den här gruppen och välj sedan **ta bort**.

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Vad är Azure Active Directory?](active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
