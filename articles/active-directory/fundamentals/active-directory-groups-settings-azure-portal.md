---
title: Redigera gruppinformation – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du redigerar din grupps information med hjälp av Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561879"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Redigera gruppinformation med hjälp av Azure Active Directory

Med Azure Active Directory (Azure AD) kan redigera du inställningarna för en grupp, inklusive uppdatera dess namn, beskrivning eller medlemskap text.

## <a name="to-edit-your-group-settings"></a>Så här redigerar du gruppinställningarna för
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **grupper**.

    Den **grupper – alla grupper** visas, som visar alla aktiva grupperna.

3. Från den **grupper – alla grupper** anger så mycket av namnet som du kan göra i den **Search** box. För den här artikeln, vi söker efter den **MDM princip – västra** grupp.

    Sökresultatet visas den **Search** rutan uppdateras när du skriver fler tecken.

    ![Sidan alla grupper med söktext i sökrutan](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Välj gruppen som **MDM princip – västra**, och välj sedan **egenskaper** från den **hantera** området.

    ![Sidan grupp översikt med medlems alternativ och information markerad](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Uppdatera den **allmänna inställningar** information efter behov, inklusive:

    ![För egenskapsinställningar för en grupp](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Gruppnamn.** Redigera det befintliga gruppnamnet.
    
    - **Gruppbeskrivning.** Redigera befintliga Gruppbeskrivning.

    - **Typ av grupp.** Du kan inte ändra typ av grupp när det har skapats. Ändra den **grupptyp**, måste du ta bort gruppen och skapa en ny.
    
    - **Typ av medlemskap.** Ändra medlemskap. Mer information om de olika tillgängliga medlemskaps typerna finns i [How to: Skapa en grundläggande grupp och Lägg till medlemmar med hjälp av](active-directory-groups-create-azure-portal.md)Azure Active Directory portalen.
    
    - **Objekt-ID.** Du kan inte ändra objekt-ID, men du kan kopiera och använda det i din PowerShell-kommandon för gruppen. Mer information om hur du använder PowerShell-cmdlets finns i [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)

- [Lägga till eller ta bort medlemmar från en grupp](active-directory-groups-members-azure-portal.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)

- [Hantera åtkomst till resurser med grupper](active-directory-manage-groups.md)

- [Associera eller lägg till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
