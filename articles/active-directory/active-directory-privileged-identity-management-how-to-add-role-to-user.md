---
title: "Lägga till eller ta bort en användarroll | Microsoft Docs"
description: "Lär dig mer om att lägga till roller för privilegierade identiteter med Azure Active Directory Privileged Identity Management-programmet."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 6a47ced8-cf34-4ce8-bea2-e4fc548cfe22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim;oldportal;it-pro;
ms.openlocfilehash: 3ac07bb7b070f44595c099a454b3d0dbc66126c9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management, lägg till eller ta bort en användarroll
Med Azure Active Directory (AD), en global administratör (eller företagets administratör) kan uppdatera som användare kan **permanent** tilldelade roller i Azure AD. Detta görs med PowerShell-cmdlets som `Add-MsolRoleMember` och `Remove-MsolRoleMember`. De kan också använda den klassiska Azure-portalen enligt beskrivningen i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles.md).

Programmet Azure AD Privileged Identity Management administratörer Privilegierade roller kan göra permanenta rolltilldelningar samt. Dessutom Privilegierade rollen administratörer kan utföra **berättigade** för administratörsroller. En berättigad administratör kan aktivera rollen när de behöver den och sedan deras behörigheter ut när de är klar.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Hantera roller med PIM i Azure-portalen
I din organisation, kan du tilldela användare för olika administrativa roller i Azure AD och Office 365 och andra Microsoft-tjänster och program.  Mer information om tillgängliga roller kan hittas på [roller i Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Om du vill lägga till eller ta bort en användare i en roll med hjälp av Privileged Identity Management, visa PIM-instrumentpanelen. Klicka sedan på den **användare i administratörsroller** knappen eller välj en specifik roll (till exempel Global administratör) från tabellen roller.

> [!NOTE]
> Om du inte har aktiverat PIM i Azure portal ännu, går du till [Kom igång med Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) mer information.

Om du vill ge en annan användare tillgång till PIM själva roller som PIM kräver att användaren har beskrivs ytterligare i [ge åtkomst till PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Lägga till en användare till en roll
1. I den [Azure-portalen](https://portal.azure.com/), Välj den **Azure AD Privileged Identity Management** panelen på instrumentpanelen.
2. Välj **hantera Privilegierade roller**.
3. I den **Rollsammanfattning** tabell, väljer du den roll som du vill hantera.
4. I bladet roll väljer **Lägg till**.
5. Klicka på **Välj användare** och Sök efter användare på den **Välj användare** bladet.  
6. Välj användaren från listan över sökresultat och klicka på **klar**.
7. Klicka på **OK** att spara ditt val. Användare som du har valt visas i listan som är berättigade till rollen.

> [!NOTE]
> Nya användare i en roll är bara tillgängliga för rollen som standard. Klicka på användare i listan om du vill att rollen permanent. Användarens information visas i ett nytt blad. Välj **Se behörighet** i menyn användaren information.  
> Om en användare kan registrera dig för Azure Multi-Factor Authentication (MFA) eller använder ett Microsoft-konto (vanligtvis @outlook.com), måste du se dem permanent i deras roller. Berättigad administratörer uppmanas att registrera sig för MFA vid aktivering.

Nu när användaren är berättigad till en roll, meddela dem om att de kan aktivera enligt anvisningarna i [så här aktiverar eller inaktiverar du en roll](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Ta bort en användare från en roll
Du kan ta bort användare från berättigade rolltilldelningar, men kontrollera att det finns alltid minst en användare som är en permanent global administratör.

Följ dessa steg om du vill ta bort en viss användare från en roll:

1. Navigera till rollen i listan roll genom att välja en roll i Azure AD PIM-instrumentpanelen eller genom att klicka på den **användare i administratörsroller** knappen.
2. Klicka på användare i användarlistan.
3. Klicka på **ta bort**. Ett meddelande som ber dig att bekräfta.
4. Klicka på **Ja** att ta bort rollen från användaren.

Om du inte vet vilka användarna måste fortfarande sina rolltilldelningar sedan kan du [startar en åtkomst-granskning för rollen](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

