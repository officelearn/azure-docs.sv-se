---
title: Lämna en organisation som gästanvändare - Azure Active Directory
description: Visar hur en Azure AD B2B-gästanvändare kan lämna en organisation med hjälp av åtkomstpanelen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272501"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Lämna en organisation som gästanvändare

En Azure Active Directory (Azure AD) B2B-gästanvändare kan när som helst välja att lämna en organisation om de inte längre behöver använda appar från den organisationen eller underhålla någon association. En användare kan lämna en organisation på egen hand, utan att behöva kontakta en administratör.

> [!NOTE]
> En gästanvändare kan inte lämna en organisation om deras konto är inaktiverat i antingen hemklienten eller resursklienten. Om deras konto är inaktiverat måste gästanvändaren kontakta klientadministratören, som antingen kan ta bort gästkontot eller aktivera gästkontot så att användaren kan lämna organisationen.

## <a name="leave-an-organization"></a>Lämna en organisation

Så här lämnar du en organisation.

1. Gå till sidan Profil i Access Panel genom att göra något av följande:
   
   - I [Azure-portalen](https://portal.azure.com)klickar du på ditt namn längst upp till höger och väljer **Visa konto**.
   - Öppna [åtkomstpanelen,](https://myapps.microsoft.com)klicka på ditt namn längst upp till höger och bredvid Organisationer väljer du **inställningsikonen**(växel).
 
   ![Skärmbild som visar användarinställningar på Åtkomstpanelen](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Om du inte redan är inloggad på den organisation som du vill lämna klickar du på länken Logga in under **Organisationer** **för att lämna organisationslänken** bredvid organisationens namn. När du har loggat in klickar du på ditt namn igen i det övre högra och bredvid Organisationer väljer du **inställningsikonen**(växel).

3. Leta reda på den organisation som du vill lämna under **Organisationer**och välj **Lämna organisation**.

   ![Skärmbild som visar alternativet Lämna organisation i användargränssnittet](media/leave-the-organization/LeaveOrg.png)

4. När du uppmanas att bekräfta väljer du **Lämna**. 

## <a name="account-removal"></a>Borttagning av konto

När en användare lämnar en organisation är användarkontot "mjukt borttaget" i katalogen. Som standard flyttas användarobjektet till området **Borttagna användare** i Azure AD men tas inte bort permanent på 30 dagar. Med den här mjuka borttagningen kan administratören återställa användarkontot (inklusive grupper och behörigheter) om användaren begär att kontot ska återställas inom 30-dagarsperioden.

Om så önskas kan en klientadministratör ta bort kontot permanent när som helst under 30-dagarsperioden. Gör så här:

1. Välj Azure Active **Directory**i [Azure-portalen](https://portal.azure.com).
2. Under **Hantera** väljer du **Användare**.
3. Välj **Borttagna användare**.
4. Markera kryssrutan bredvid en borttagen användare och välj sedan **Ta bort permanent**.

Om du tar bort en användare permanent är den här åtgärden oåterkallelig.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)



