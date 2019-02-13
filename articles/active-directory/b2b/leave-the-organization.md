---
title: Lämna en organisation som gästanvändare – Azure Active Directory | Microsoft Docs
description: Visar hur en Azure AD B2B-gästanvändare lämna en organisation med hjälp av åtkomstpanelen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d8183f7fa55a410b44d33295ad24f0f07adb145
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185935"
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
2. Under **Hantera** väljer du **Användare**.
3. Välj **borttagna användare**.
4. Markera kryssrutan bredvid en borttagen användare och välj sedan **ta bort permanent**.

Om du permanent ta bort en användare är den här åtgärden oåterkallelig.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [vad är Azure AD B2B-samarbete?](what-is-b2b.md)



