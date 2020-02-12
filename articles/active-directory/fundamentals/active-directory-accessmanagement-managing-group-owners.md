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
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149855"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Lägg till eller ta bort gruppägare i Azure Active Directory
Azure Active Directory (Azure AD)-grupper ägs och hanteras av gruppägare. Grupp ägare kan vara användare eller tjänstens huvud namn och kan hantera gruppen inklusive medlemskap. Endast befintliga grupp ägare eller grupp hanterings administratörer kan tilldela grupp ägare. Gruppägare behöver inte vara medlemmar i gruppen.

När en grupp saknar ägare kan grupp hanterings administratörer fortfarande hantera gruppen. Vi rekommenderar att varje grupp har minst en ägare. När ägarna är koppla till en grupp kan inte den sista ägaren av gruppen tas bort. Var noga med att välja en annan ägare innan du tar bort den sista ägaren från gruppen.

## <a name="add-an-owner-to-a-group"></a>Lägg till en ägare till en grupp
Nedan visas instruktioner för att lägga till en användare som ägare till en grupp med hjälp av Azure AD-portalen. Om du vill lägga till ett huvud namn för tjänsten som ägare av en grupp följer du anvisningarna för att göra det med hjälp av [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Att lägga till gruppägare
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, Välj **grupper**och välj sedan den grupp som du vill lägga till en ägare för (i det här exemplet *MDM-princip – väst*).

3. På **översikts sidan för MDM-policyn** väljer du **ägare**.

    ![Princip för MDM – västra översiktssidan med ägare alternativet markerat](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. På sidan **MDM-princip – väst-ägare** väljer du **Lägg till ägare**och söker sedan efter och väljer den användare som ska vara den nya grupp ägaren och väljer sedan **Välj**.

    ![MDM - Väst - ägare sida med Lägg till ägare alternativet är markerat](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    När du har valt den nya ägaren kan du uppdatera sidan **ägare** och se namnet som lagts till i listan över ägare.

## <a name="remove-an-owner-from-a-group"></a>Ta bort ägare från en grupp
Ta bort ägare från en grupp med hjälp av Azure AD.

### <a name="to-remove-an-owner"></a>Ta bort en ägare
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, Välj **grupper**och välj sedan den grupp som du vill ta bort en ägare för (i det här exemplet *MDM-princip – väst*).

3. På **översikts sidan för MDM-policyn** väljer du **ägare**.

    ![Princip för MDM – västra översiktssidan med ägare alternativet markerat](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. På sidan **MDM-princip – väst-ägare** väljer du den användare som du vill ta bort som grupp ägare, väljer **ta bort** på sidan med användarens information och väljer **Ja** för att bekräfta ditt beslut.

    ![Sidan med information för användaren med ta bort alternativet markerat](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    När du har tagit bort ägaren kan du gå tillbaka till sidan **ägare** och se att namnet har tagits bort från listan över ägare.

## <a name="next-steps"></a>Nästa steg
- [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)

- [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-cmdlets.md)

- [Använda grupper för att tilldela åtkomst till en integrerad SaaS-app](../users-groups-roles/groups-saasapps.md)

- [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-v2-cmdlets.md)
