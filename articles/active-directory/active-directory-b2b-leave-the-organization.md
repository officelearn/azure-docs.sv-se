---
title: Lämna en organisation som gästanvändare - Azure Active Directory | Microsoft Docs
description: Visar hur en Azure AD B2B-gästanvändaren kan lämna en organisation med hjälp av åtkomstpanelen.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 942439da3e116415c77a28950df69d44744b2dd8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077545"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Lämna en organisation som gästanvändare

Ett Azure Active Directory (AD Azure) B2B gästanvändaren kan välja att lämna en organisation när som helst om de inte längre behöver använda appar från organisationens eller underhålla eventuella kopplingar. En användare kan lämna en organisation på egen hand, utan att behöva kontakta en administratör.

## <a name="leave-an-organization"></a>Lämna en organisation

Lämna en organisation, som en användare som har loggat in på den [åtkomstpanelen](https://myapps.microsoft.com), gör du följande:

1. Om du inte redan är inloggad på den organisation som du vill lämna markerar du namnet i det övre högra hörnet och klicka på den organisation som du vill lämna.
2. Välj ditt namn i det övre högra hörnet.
3. Bredvid **organisationer**, välja inställningsikonen (kugghjulet).
 
   ![Skärmbild som visar användarinställningar i åtkomstpanelen](media/active-directory-b2b-leave-the-organization/UserSettings.png) 

3. Under **organisationer**, hitta den organisation som du vill lämna och välj **lämnar organisationen**.

   ![Skärmbild som visar lämnar organisationen alternativet i användargränssnittet](media/active-directory-b2b-leave-the-organization/LeaveOrg.png)

4. När du ombeds bekräfta Välj **lämna**. 

## <a name="account-removal"></a>Borttagning av kontot

När en användare lämnar organisationen, användarkontot är ”Mjuk Borttagen” i katalogen. Som standard användarobjektet flyttas till den **bort användare** område i Azure AD, men är inte permanent bort i 30 dagar. Mjuk borttagning gör det möjligt för administratören att återställa användarkontot (inklusive grupper och behörigheter) om användaren gör en begäran om att återställa kontot inom 30-dagarsperiod.

Om du vill Innehavaradministratör kan permanent ta bort kontot när som helst under 30-dagarsperiod. Gör så här:

1. I den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory**.
2. Under **hantera**väljer **användare**.
3. Välj **bort användare**.
4. Markera kryssrutan bredvid en borttagen användare och välj sedan **ta bort permanent**.

Om du permanent ta bort en användare är med den här åtgärden oåterkalleligt.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns [vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)



