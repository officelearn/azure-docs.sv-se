---
title: Lägga till eller ta bort en användarroll | Microsoft Docs
description: Lär dig mer om att lägga till roller i Privilegierade identiteter med Azure Active Directory Privileged Identity Management-programmet.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 01/03/2018
ms.author: rolyon
ms.openlocfilehash: 438a2fcd511fe62fa1dc7ba603c3770d5bcb56a4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446851"
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management, lägg till eller ta bort en användarroll
Med Azure Active Directory (AD), en global administratör (eller företagets administratör) kan uppdatera som användarna **permanent** tilldelas till roller i Azure AD. Detta görs med PowerShell-cmdlets som `Add-MsolRoleMember` och `Remove-MsolRoleMember`. Eller de kan använda Azure portal enligt beskrivningen i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles.md).

Azure AD Privileged Identity Management-programmet administratörer Privilegierade roller kan göra permanenta rolltilldelningar, samt. Dessutom Privilegierade rolladministratörer kan utse användare **berättigade** för administratörsroller. En berättigad administratör kan aktivera rollen när de behöver den, och sedan deras behörigheter går ut när de är klar.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Hantera roller med PIM i Azure portal
I din organisation, kan du tilldela användare till olika administrativa roller i Azure AD, Office 365 och andra Microsoft-tjänster och program.  Mer information om tillgängliga roller finns på [roller i Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Om du vill lägga till eller ta bort en användare i en roll med hjälp av Privileged Identity Management genom att ta fram PIM-instrumentpanelen. Klicka sedan antingen på den **användare i Adminroller** knappen eller välja en viss roll (till exempel Global administratör) från tabellen roller.

> [!NOTE]
> Om du inte har aktiverat PIM i Azure portal ännu, går du till [Kom igång med Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) mer information.

Om du vill ge en annan användaråtkomst till PIM själva de roller som PIM kräver att användaren har beskrivs ytterligare i [ge åtkomst till PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Lägga till en användare till en roll
1. I den [Azure-portalen](https://portal.azure.com/)väljer den **Azure AD Privileged Identity Management** panelen på instrumentpanelen.
2. Välj **hantera Privilegierade roller**.
3. I den **Rollsammanfattning** tabellen, Välj den roll som du vill hantera.
4. I bladet roll väljer **Lägg till**.
5. Klicka på **Välj användare** och Sök efter användaren på den **Välj användare** bladet.  
6. Välj användaren från listan över sökresultat och klicka på **klar**.
7. Klicka på **OK** att spara ditt val. Användare som du har valt visas i listan som berättigad för rollen.

> [!NOTE]
> Nya användare i en roll är bara berättigad till rollen som standard. Om du vill göra rollen permanenta, klickar du på användaren i listan. Information om användaren visas i ett nytt blad. Välj **gör permanent** i menyn användaren information.  
> Om en användare kan inte registrera dig för Azure Multi-Factor Authentication (MFA) eller använder ett Microsoft-konto (vanligtvis @outlook.com), måste du se dem permanent i deras roller. Berättigade administratörer uppmanas att registrera för MFA vid aktivering.

Nu när du är berättigad för en roll kan informera dem om att de kan aktivera enligt anvisningarna i [aktivera eller inaktivera en roll](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Ta bort en användare från en roll
Du kan ta bort användare från kvalificerade rolltilldelningar, men se till att det finns alltid minst en användare som är en permanent global administratör.

Följ dessa steg för att ta bort en specifik användare från en roll:

1. Navigera till rollen i listan över rollen genom att välja en roll i Azure AD PIM-instrumentpanel eller genom att klicka på den **användare i Adminroller** knappen.
2. Klicka på användare i användarlistan.
3. Klicka på **ta bort**. Ett meddelande som ber dig att bekräfta.
4. Klicka på **Ja** att ta bort rollen från användaren.

Om du inte vet vilka användare behöver fortfarande ha sin rolltilldelningar så kan du [starta en åtkomstgranskning för rollen](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

