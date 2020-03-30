---
title: Redigera gruppinformationen – Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du redigerar gruppens information med Hjälp av Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561879"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Redigera gruppinformationen med Hjälp av Azure Active Directory

Med Hjälp av Azure Active Directory (Azure AD) kan du redigera en grupps inställningar, inklusive uppdatering av dess namn, beskrivning eller medlemstyp.

## <a name="to-edit-your-group-settings"></a>Så här redigerar du gruppinställningarna
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **Grupper**.

    Sidan **Grupper - Alla grupper** visas som visar alla dina aktiva grupper.

3. På sidan **Grupper - Alla grupper** skriver du så mycket av gruppnamnet som du kan i **sökrutan.** I den här artikeln söker vi efter **MDM-principen - West-gruppen.**

    Sökresultaten visas under **sökrutan** och uppdateras när du skriver fler tecken.

    ![Sidan Alla grupper med söktext i rutan Sök](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Välj **grupp-MDM-principen - Väst**och välj sedan **Egenskaper** i området **Hantera.**

    ![Sidan Gruppöversikt, med alternativet Medlem och information markerad](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Uppdatera informationen **om allmänna inställningar** efter behov, inklusive:

    ![Egenskaper inställningar för en grupp](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Gruppens namn.** Redigera det befintliga gruppnamnet.
    
    - **Gruppbeskrivning.** Redigera den befintliga gruppbeskrivningen.

    - **Grupptyp.** Du kan inte ändra typen av grupp när den har skapats. Om du vill ändra **grupptypen**måste du ta bort gruppen och skapa en ny.
    
    - **Typ av medlemskap.** Ändra medlemstypen. Mer information om de olika tillgängliga medlemstyperna finns i [Så här skapar du en grundläggande grupp och lägger till medlemmar med Azure Active Directory-portalen](active-directory-groups-create-azure-portal.md).
    
    - **Objekt-ID.** Du kan inte ändra objekt-ID: t, men du kan kopiera det som ska användas i PowerShell-kommandona för gruppen. Mer information om hur du använder PowerShell-cmdletar finns i [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

- [Så här lägger du till eller tar bort medlemmar från en grupp](active-directory-groups-members-azure-portal.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)

- [Hantera åtkomst till resurser med grupper](active-directory-manage-groups.md)

- [Associera eller lägg till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
