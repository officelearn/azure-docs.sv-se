---
title: Så här redigerar du din gruppinformation med Azure Active Directory | Microsoft Docs
description: Så här redigerar du en grupp information med hjälp av Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 0bac740a5da9c1e57ea1755c58579463da4a883a
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580454"
---
# <a name="how-to-edit-your-group-information-using-azure-active-directory"></a>Så här: redigera gruppinformation med hjälp av Azure Active Directory

Med Azure Active Directory kan redigera du inställningarna för en grupp, inklusive uppdatera dess namn, beskrivning eller Medlemskapstyp.

## <a name="to-edit-your-group-settings"></a>Så här redigerar du gruppinställningarna för
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som Global administratör för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **grupper**.

    Den **grupper – alla grupper** visas, som visar alla aktiva grupperna.

3. Från den **grupper – alla grupper** anger så mycket av namnet som du kan göra i den **Search** box. För den här artikeln, vi söker efter den **MDM princip – västra** grupp.

    Sökresultatet visas den **Search** rutan uppdateras när du skriver fler tecken.

    ![Sidan alla grupper med söktext i sökrutan](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Välj gruppen som **MDM princip – västra**, och välj sedan **egenskaper** från den **hantera** området.

    ![Översikt översiktssidan med numret och medlemmar och medlemmen alternativet markerat](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Uppdatera den **allmänna inställningar** information efter behov, inklusive:

    ![För egenskapsinställningar för en grupp](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Gruppnamn.** Redigera det befintliga gruppnamnet.
    
    - **Beskrivning av gruppen.** Redigera befintliga Gruppbeskrivning.

    - **Typ av grupp.** Du kan inte ändra typ av grupp när det har skapats. Ändra den **grupptyp**, måste du ta bort gruppen och skapa en ny.
    
    - **Typ av medlemskap.** Ändra medlemskap. Mer information om de olika typerna av tillgängliga medlemskap finns [så här: skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory-portalen](active-directory-groups-create-azure-portal.md)
    
    - **Objekt-ID.** Du kan inte ändra objekt-ID, men du kan kopiera och använda det i din PowerShell-kommandon för gruppen. Mer information om hur du använder PowerShell-cmdlets finns i [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)

- [Lägga till eller ta bort medlemmar från en grupp](active-directory-groups-members-azure-portal.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)

- [Hantera åtkomst till resurser med hjälp av grupper](active-directory-manage-groups.md)

- [Koppla eller lägga till en Azure-prenumeration i Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
