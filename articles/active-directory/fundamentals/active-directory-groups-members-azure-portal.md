---
title: Lägga till eller ta bort gruppmedlemmar – Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du lägger till eller tar bort medlemmar från en grupp med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561969"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Lägga till eller ta bort gruppmedlemmar med Azure Active Directory
Med Hjälp av Azure Active Directory kan du fortsätta att lägga till och ta bort gruppmedlemmar.

## <a name="to-add-group-members"></a>Så här lägger du till gruppmedlemmar

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **Grupper**.

3. På sidan **Grupper - Alla grupper** söker du efter och väljer den grupp som du vill lägga till medlemmen i. I det här fallet använder du vår tidigare skapade grupp, **MDM-policy - Väst.**

    ![Sidan Grupper-Alla grupper, gruppnamnet markerat](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. På **översiktssidan för MDM policy – West** väljer du **Medlemmar** i området **Hantera**.

    ![MDM-policy - Sidan Västöversikt, med alternativet Medlemmar markerat](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Välj **Lägg till medlemmar**och sök sedan och välj var och en av de medlemmar som du vill lägga till i gruppen och välj sedan **Välj**.

    Du får ett meddelande om att medlemmarna har lagts till.

    ![Sidan Lägg till medlemmar, med sökte efter medlem visad](media/active-directory-groups-members-azure-portal/update-members.png)

6. Uppdatera skärmen om du vill se alla medlemsnamn som läggs till i gruppen.

## <a name="to-remove-group-members"></a>Så här tar du bort gruppmedlemmar

1. På sidan **Grupper - Alla grupper** söker du efter och väljer den grupp som du vill ta bort medlemmen från. Återigen kommer vi att använda, **MDM politik - West**.

2. Välj **Medlemmar** i området **Hantera,** sök efter och välj namnet på den medlem som ska tas bort och välj sedan **Ta bort**.

    ![Sidan Medlemsinformation, med alternativet Ta bort](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Nästa steg

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Redigera dina gruppinställningar](active-directory-groups-settings-azure-portal.md)

- [Hantera åtkomst till resurser med grupper](active-directory-manage-groups.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Associera eller lägg till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
