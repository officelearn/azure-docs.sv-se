---
title: Ta bort en grupp-Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tar bort en grupp med hjälp av Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "68561909"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Ta bort en grupp med Azure Active Directory
Du kan ta bort en Azure Active Directory (Azure AD) grupp av valfritt antal orsaker, men vanligt vis är det att du:

- Felaktigt angett **grupp typen** till fel alternativ.

- Fel eller en dubblett av gruppen skapades av misstag. 

- Behöver inte längre gruppen.

## <a name="to-delete-a-group"></a>Ta bort en grupp
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **grupper**.

3. Från sidan **grupper – alla grupper** söker du efter och väljer den grupp som du vill ta bort. I de här stegen använder vi **MDM-princip – öst**.

    ![Grupper – sidan alla grupper, grupp namn markerat](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. På sidan **MDM-princip – östra översikt** och välj sedan **ta bort**.

    Gruppen tas bort från Azure Active Directory klient organisationen.

    ![MDM-princip – sidan för östra översikt, alternativet ta bort markerat](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Nästa steg

- Om du tar bort en grupp av misstag kan du skapa den igen. Mer information finns i [så här skapar du en grundläggande grupp och lägger till medlemmar](active-directory-groups-create-azure-portal.md).

- Om du tar bort en Office 365-grupp av misstag kanske du kan återställa den. Mer information finns i [återställa en borttagen Office 365-grupp](../users-groups-roles/groups-restore-deleted.md).
