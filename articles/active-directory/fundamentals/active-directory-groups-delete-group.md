---
title: Ta bort en grupp – Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du tar bort en grupp med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561909"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Ta bort en grupp med Azure Active Directory
Du kan ta bort en Azure Active Directory-grupp (Azure AD) av många olika anledningar, men vanligtvis beror det på att du:

- Felaktigt ange **grupptypen** till fel alternativ.

- Skapade fel eller en dubblettgrupp av misstag. 

- Inte längre behöver gruppen.

## <a name="to-delete-a-group"></a>Så här tar du bort en grupp
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **Grupper**.

3. Sök efter och välj den grupp som du vill ta bort på sidan **Grupper - alla grupper.** För dessa steg använder vi **MDM-principen - Öst**.

    ![Sidan Grupper-Alla grupper, gruppnamnet markerat](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. På sidan **MDM-princip - Östöversikt** och välj sedan **Ta bort**.

    Gruppen tas bort från din Azure Active Directory-klient.

    ![MDM-princip - Sidan Östöversikt, borttagningsalternativ markerat](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Nästa steg

- Om du tar bort en grupp av misstag kan du skapa den igen. Mer information finns i [Så här skapar du en grundläggande grupp och lägger till medlemmar](active-directory-groups-create-azure-portal.md).

- Om du tar bort en Office 365-grupp av misstag kan du kanske återställa den. Mer information finns i [Återställa en borttagen Office 365-grupp](../users-groups-roles/groups-restore-deleted.md).
