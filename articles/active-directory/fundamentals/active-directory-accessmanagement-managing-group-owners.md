---
title: Lägga till eller ta bort gruppägare - Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du lägger till eller tar bort gruppägare med Hjälp av Azure Active Directory.
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
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149855"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Lägga till eller ta bort gruppägare i Azure Active Directory
Azure Active Directory (Azure AD) grupper ägs och hanteras av gruppägare. Gruppägare kan vara användare eller tjänsthuvudnamn och kan hantera gruppen inklusive medlemskap. Endast befintliga gruppägare eller grupphanteringsadministratörer kan tilldela gruppägare. Gruppägare behöver inte vara medlemmar i gruppen.

När en grupp inte har någon ägare kan grupphanteraradministratörer fortfarande hantera gruppen. Det rekommenderas för varje grupp att ha minst en ägare. När ägare harginerats för en grupp kan inte den sista ägaren av gruppen tas bort. Se till att välja en annan ägare innan du tar bort den sista ägaren från gruppen.

## <a name="add-an-owner-to-a-group"></a>Lägga till en ägare i en grupp
Nedan finns instruktioner för hur du lägger till en användare som ägare i en grupp med Hjälp av Azure AD-portalen. Om du vill lägga till ett tjänsthuvudnamn som ägare till en grupp följer du instruktionerna för att göra det med [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Så här lägger du till en gruppägare
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, välj **Grupper**och välj sedan den grupp som du vill lägga till en ägare för (i det här exemplet *MDM-principen - Väst).*

3. På sidan **MDM-princip - Västöversikt** väljer du **Ägare**.

    ![MDM-princip - Sidan Västöversikt med alternativet Ägare markerat](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. På sidan **MDM-princip - Väst - Ägare** väljer du Lägg till **ägare**och söker sedan efter och väljer den användare som ska vara den nya gruppägaren och väljer sedan **Välj**.

    ![MDM-policy - Väst - Ägarsida med Alternativet Lägg till ägare markerat](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    När du har valt den nya ägaren kan du uppdatera sidan **Ägare** och se namnet som lagts till i ägarlistan.

## <a name="remove-an-owner-from-a-group"></a>Ta bort en ägare från en grupp
Ta bort en ägare från en grupp med Azure AD.

### <a name="to-remove-an-owner"></a>Så här tar du bort en ägare
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, välj **Grupper**och välj sedan den grupp som du vill ta bort en ägare för (i det här exemplet *MDM-principen - Väst).*

3. På sidan **MDM-princip - Västöversikt** väljer du **Ägare**.

    ![MDM-princip - Sidan Västöversikt med alternativet Ägare markerat](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. På sidan **MDM-princip - Väst - Ägare** väljer du den användare som du vill ta bort som gruppägare, väljer Ta **bort** från användarens informationssida och väljer **Ja** för att bekräfta ditt beslut.

    ![Användarens informationssida med alternativet Ta bort markerat](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    När du har tagit bort ägaren kan du gå tillbaka till sidan **Ägare** och se att namnet har tagits bort från listan över ägare.

## <a name="next-steps"></a>Nästa steg
- [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)

- [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md)

- [Använda grupper för att tilldela åtkomst till en integrerad SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-v2-cmdlets.md)
