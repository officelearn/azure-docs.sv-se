---
title: Lägg till eller ta bort gruppägare - Azure Active Directory | Microsoft Docs
description: Instruktioner om hur du lägger till eller ta bort gruppen ägare med hjälp av Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d259be0c04af0fcf3628a9f296730749404610cb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562089"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Lägg till eller ta bort gruppägare i Azure Active Directory
Azure Active Directory (Azure AD)-grupper ägs och hanteras av gruppägare. Grupp ägare kan vara användare eller tjänstens huvud namn och kan hantera gruppen inklusive medlemskap. Endast befintliga grupp ägare eller grupp hanterings administratörer kan tilldela grupp onwers. Gruppägare behöver inte vara medlemmar i gruppen.

När en grupp saknar ägare kan grupp hanterings administratörer fortfarande hantera gruppen.

## <a name="add-an-owner-to-a-group"></a>Lägg till en ägare till en grupp
Nedan visas instruktioner för att lägga till en användare som ägare till en grupp med hjälp av Azure AD-portalen. Om du vill lägga till ett huvud namn för tjänsten som ägare av en grupp följer du anvisningarna för att göra det med hjälp av [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Att lägga till gruppägare
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**väljer **grupper**, och väljer sedan den grupp som du vill lägga till en ägare (i det här exemplet *MDM princip – västra*).

3. På den **MDM princip – västra översikt** väljer **ägare**.

    ![Princip för MDM – västra översiktssidan med ägare alternativet markerat](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. På den **MDM och ägare av Grupprincip - Väst -** väljer **lägga till ägare**, och sök sedan efter och välj den användare som ska vara den nya gruppägaren och välj sedan **Välj**.

    ![MDM - Väst - ägare sida med Lägg till ägare alternativet är markerat](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    När du väljer den nya ägaren måste du uppdatera den **ägare** sidan och se namn läggs till i listan över ägare.

## <a name="remove-an-owner-from-a-group"></a>Ta bort ägare från en grupp
Ta bort ägare från en grupp med hjälp av Azure AD.

### <a name="to-remove-an-owner"></a>Ta bort en ägare
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**väljer **grupper**, och väljer sedan den grupp som du vill ta bort en ägare (i det här exemplet *MDM princip – västra*).

3. På den **MDM princip – västra översikt** väljer **ägare**.

    ![Princip för MDM – västra översiktssidan med ägare alternativet markerat](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. På den **MDM och ägare av Grupprincip - Väst -** väljer du den användare som du vill ta bort som gruppägare, Välj **ta bort** användarens informationssida och välj **Ja** att bekräfta ditt beslut.

    ![Sidan med information för användaren med ta bort alternativet markerat](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    När du tar bort ägaren kan du gå tillbaka till den **ägare** och se namnet har tagits bort från listan över ägare.

## <a name="next-steps"></a>Nästa steg
- [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)

- [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md)

- [Använda grupper för att tilldela åtkomst till en integrerad SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-v2-cmdlets.md)
