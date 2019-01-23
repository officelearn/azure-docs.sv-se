---
title: Ta bort en grupp – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tar bort en grupp med Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 0e50abb4c7aa5641d92bf0a9f5c4516fc29125e2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452584"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Ta bort en grupp med Azure Active Directory
Du kan ta bort en grupp för Azure Active Directory (AD Azure) till många olika orsaker, men vanligtvis det kommer att du:

- Ange ett felaktigt sätt den **grupptyp** till fel alternativ.

- Skapa fel eller en dublettgrupp av misstag. 

- Behöver inte längre i gruppen.

## <a name="to-delete-a-group"></a>Ta bort en grupp
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **grupper**.

3. Från den **grupper – alla grupper** , söka efter och välj den grupp som du vill ta bort. För de här stegen, använder vi **MDM princip – östra**.

    ![Grupper – alla gruppsidan gruppnamn markerat](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. På den **MDM princip – östra översikt** och välj sedan **ta bort**.

    Ta bort gruppen från Azure Active Directory-klient.

    ![MDM-principen – östra översiktssidan, alternativet Ta bort markerade](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Nästa steg

- Om du tar bort en grupp av misstag kan skapa du det igen. Mer information finns i [hur du skapar en grundläggande grupp och Lägg till medlemmar](active-directory-groups-create-azure-portal.md).

- Om du tar bort en Office 365-grupp av misstag kan kanske du kan du återställa den. Mer information finns i [återställa en borttagen Office 365-grupp](../users-groups-roles/groups-restore-deleted.md).
