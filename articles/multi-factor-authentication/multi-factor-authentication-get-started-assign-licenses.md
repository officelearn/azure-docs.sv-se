---
title: "Tilldela licenser för Azure MFA | Microsoft Docs"
description: "Lär dig hur du tilldelar användarlicenser för Microsoft Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ROBOTS: NOINDEX
ms.openlocfilehash: 45522bf526c4aeab1d6ccc8891a55a0436ff9320
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Tilldela en Azure MFA-, Azure AD Premium- eller Enterprise Mobility-licens till användare
Om du har köpt Azure MFA-, Azure AD Premium- eller Enterprise Mobility Suite-licenser behöver du inte skapa någon Multi-Factor Authentication-provider. När du tilldelar licenser till dina användare kan du börja aktivera dem för MFA.

## <a name="to-assign-a-license"></a>Tilldela en licens
1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör.
2. Välj **Active Directory** till vänster.
3. På Active Directory-sidan dubbelklickar du på katalogen som innehåller de användare som du vill aktivera.
4. Överst på katalogsidan väljer du **Licenser**.
   ![Tilldela licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. På sidan Licenser väljer du **Azure Multi-Factor Authentication**, **Active Directory Premium** eller **Enterprise Mobility Suite**.  Om du bara har en så har den valts automatiskt.
6. Klicka på **Tilldela** längst ned på sidan.
   ![Tilldela licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. I rutan som visas klickar du bredvid de användare eller grupper som du vill tilldela licenser.  En grön bockmarkering bör visas.
8. Spara ändringarna genom att klicka på bockmarkeringen.
   ![Tilldela licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. Du bör se ett meddelande som anger hur många licenser som har tilldelats och hur många som kanske har misslyckats.  Klicka på **OK**.
   ![Tilldela licenser](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Vad är Microsoft Azure Active Directory-licensiering?](../active-directory/active-directory-licensing-what-is.md) (på engelska)
