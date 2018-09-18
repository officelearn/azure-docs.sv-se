---
title: Lämna en organisation som gästanvändare – Azure Active Directory | Microsoft Docs
description: Visar hur en Azure AD B2B-gästanvändare lämna en organisation med hjälp av åtkomstpanelen.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: cea882bd1ba2ba12d34690fb47ec1afd6edf5c4c
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982178"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Lämna en organisation som gästanvändare

En Azure Active Directory (Azure AD) B2B-gästanvändare kan välja att lämna en organisation när som helst om de inte längre behöver använda appar från den organisationen eller underhålla eventuella kopplingar. En användare kan lämna en organisation på egen hand, utan att behöva kontakta en administratör.

## <a name="leave-an-organization"></a>Lämna en organisation

Lämna en organisation, som en användare som loggat in på den [åtkomstpanelen](https://myapps.microsoft.com), gör du följande:

1. Om du inte redan är inloggad på den organisation som du vill lämna, markerar du namnet i det övre högra hörnet och klicka på den organisation som du vill lämna.
2. Välj ditt namn i det övre högra hörnet.
3. Bredvid **organisationer**, välja inställningsikonen (kugghjulet).
 
   ![Skärmbild som visar användarinställningar i åtkomstpanelen](media/leave-the-organization/UserSettings.png) 

3. Under **organisationer**, hitta den organisation som du vill lämna och välj **lämna organisationen**.

   ![Skärmbild som visar lämna organisationen alternativet i användargränssnittet](media/leave-the-organization/LeaveOrg.png)

4. När du ombeds bekräfta Välj **lämna**. 

## <a name="account-removal"></a>Kontot tagits bort

När en användare lämnar en organisation, användarkontot ”ej är permanent borttagen” i katalogen. Som standard användarobjektet flyttas till den **borttagna användare** område i Azure AD, men inte är permanent bort i 30 dagar. Den här mjuk borttagning kan administratören återställa användarkontot (inklusive grupper och behörigheter), om användaren gör en begäran om att återställa kontot inom 30-dagarsperiod.

Om du vill kan en Innehavaradministratör kan permanent ta bort kontot när som helst under 30-dagarsperiod. Gör så här:

1. I den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory**.
2. Under **hantera**väljer **användare**.
3. Välj **borttagna användare**.
4. Markera kryssrutan bredvid en borttagen användare och välj sedan **ta bort permanent**.

Om du permanent ta bort en användare är den här åtgärden oåterkallelig.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [vad är Azure AD B2B-samarbete?](what-is-b2b.md)



