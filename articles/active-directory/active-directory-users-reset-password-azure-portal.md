---
title: Lösenordsåterställning i Azure Active Directory | Microsoft Docs
description: Administratören initierade återställning av lösenord för en användare i Azure Active Directory
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
editor: ''
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: faeb66c9d0638b13c4716062b768a9c6742edcd1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714161"
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Återställa lösenordet för en användare i Azure Active Directory

Administratörer kan behöva återställa en användares lösenord i fall där de har glömt, var låst out- eller andra scenarier. De steg som följer hur du återställer en användares lösenord.

## <a name="how-to-reset-the-password-for-a-user"></a>Hur du återställer lösenordet för en användare

1. Logga in på den [Azure AD Admin Center](https://aad.portal.azure.com) med ett konto som har behörighet att återställa användarlösenord för katalogen.
2. Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj den användare som du vill återställa lösenordet för.
2. Den valda användaren väljer **Återställ lösenord**.

    ![Återställa ett lösenord för en användare från användarprofilen i Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. På **Återställ lösenord**väljer **Återställ lösenord**.
7. Ett tillfälligt lösenord visas som du sedan kan vidarebefordra till användaren. Användaren blir ombedd att ändra sina lösenord nästa gång de loggar in. 

   > [!NOTE]
   > Den här tillfälliga lösenord inte har en förfallotid så att den kommer att gälla tills de loggar in är sedan tvingas ändra den. 

## <a name="next-steps"></a>Nästa steg
* [Lägg till en användare](active-directory-users-create-azure-portal.md)
* [Tilldela administratörsroller till en användare](active-directory-users-assign-role-azure-portal.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Tar bort en användare i Azure AD](active-directory-users-delete-user-azure-portal.md)
