---
title: Hur du tar bort en grupp med Azure Active Directory | Microsoft Docs
description: Lär dig hur du tar bort en grupp med Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 014fe487d23a6c75e94ca2708ed15044bd6cf53b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574342"
---
# <a name="how-to-delete-a-group-using-azure-active-directory"></a>Så här: ta bort en grupp med Azure Active Directory
Du kan ta bort en grupp av olika skäl, men vanligtvis det kommer att du:

- Ange ett felaktigt sätt den **grupptyp** till fel alternativ

- Skapat felaktigt eller en dublettgrupp av misstag 

- Inte längre behöver gruppen

## <a name="to-delete-a-group"></a>Ta bort en grupp
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som Global administratör för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **grupper**.

3. Från den **grupper – alla grupper** , söka efter och välj den grupp som du vill ta bort. För de här stegen, använder vi **MDM princip – östra**.

    ![Grupper – alla gruppsidan gruppnamn markerat](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. På den **MDM princip – östra översikt** och välj sedan **ta bort**.

    Ta bort gruppen från Azure Active Directory-klient.

    ![MDM-principen – östra översiktssidan, alternativet Ta bort markerade](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Nästa steg

- Om du tar bort en grupp av misstag kan skapa du det igen. Mer information finns i [hur du skapar en grundläggande grupp och Lägg till medlemmar](active-directory-groups-create-azure-portal.md).

- Om du tar bort en Office 365-grupp av misstag kan kanske du kan du återställa den. Mer information finns i [återställa en borttagen Office 365-grupp](../users-groups-roles/groups-restore-deleted.md).
