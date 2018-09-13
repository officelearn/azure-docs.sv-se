---
title: Lösenord för självbetjäning i Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 689ab264e56bc8559db6237eee19566e9e3c429f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648441"
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Återställa lösenordet för en användare i Azure Active Directory

Administratörer kan behöva återställa en användares lösenord i fall där de har glömt, låstes upp eller andra scenarier. Stegen nedan beskriver hur du återställer en användares lösenord.

## <a name="how-to-reset-the-password-for-a-user"></a>Så här återställer du lösenordet för en användare

1. Logga in på den [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som har directory behörighet att återställa användarlösenord.
2. Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj den användare som du vill återställa lösenordet för.
2. För den valda användaren väljer **Återställ lösenord**.

    ![Återställa ett lösenord för en användare från användarens profil i Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. På **Återställ lösenord**väljer **Återställ lösenord**.
7. Ett tillfälligt lösenord visas som du kan sedan vidarebefordra till användaren. Användaren uppmanas att ändra sina lösenord nästa gång de loggar in. 

   > [!NOTE]
   > Den här tillfälliga lösenordet inte har en förfallotid så att den kommer att gälla förrän användaren loggar in och är sedan tvingas att ändra den. 

## <a name="next-steps"></a>Nästa steg
* [Lägg till en användare](../add-users-azure-active-directory.md)
* [Tilldela administratörsroller till en användare](active-directory-users-assign-role-azure-portal.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Ta bort en användare i Azure AD](../add-users-azure-active-directory.md)
