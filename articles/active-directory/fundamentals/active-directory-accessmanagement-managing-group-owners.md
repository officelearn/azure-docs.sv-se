---
title: Nästa steg för access management med hjälp av grupper – Azure AD | Microsoft Docs
description: Avancerade hur – för hantering av säkerhetsgrupper och hur du använder dessa grupper för att hantera åtkomst till en resurs.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: d84c3b556f27366c249382a37e0a8227726110cf
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714692"
---
# <a name="managing-owners-for-a-group"></a>Hantera ägare för en grupp
När en resursägare har tilldelats åtkomst till en resurs till en Azure AD-grupp, hanteras medlemskap i gruppen av gruppägare. Resursägaren delegerar effektivt behörighet att tilldela användare till resursen till ägare av gruppen.

## <a name="add-an-owner-to-a-group"></a>Lägg till en ägare till en grupp

1. I den [Azure AD administratörscenter](https://aad.portal.azure.com)väljer **användare och grupper**.
2. Välj **alla grupper**, och sedan öppna den grupp som du vill lägga till ägare till.
3. Välj **lägga till ägare**.
4. På den **lägga till ägare** väljer du den användare som du vill lägga till som ägare till den här gruppen och kontrollera att namnet läggs till i **valda** fönstret.

## <a name="remove-an-owner-from-a-group"></a>Ta bort ägare från en grupp

1. I den [Azure AD administratörscenter](https://aad.portal.azure.com)väljer **användare och grupper**.
2. Välj **alla grupper**, och sedan öppna den grupp som du vill ta bort ägare.
3. Välj den **ägare** fliken.
4. Välj ägare som du vill ta bort från den här gruppen och välj sedan **ta bort**.

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../connect/active-directory-aadconnect.md)
