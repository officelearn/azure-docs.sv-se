---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471584"
---
Villkorlig åtkomst möjliggör finkornig åtkomstkontroll per program. För att kunna använda villkorlig åtkomst bör du ha Azure AD Premium 1 eller större licensiering tillämpad på de användare som omfattas av reglerna för villkorlig åtkomst.

1. Navigera till **sidan Enterprise-program - Alla program** och klicka på Azure **VPN**.

   - Klicka på **Villkorlig åtkomst**.
   - Klicka på **Ny princip** för att öppna fönstret **Nytt.**
2. I fönstret **Nytt** navigerar du till **Tilldelningar -> Användare och grupper**. På fliken **Användare och grupper ->** **Inkludera:**

   - Klicka på **Välj användare och grupper**.
   - Kontrollera **användare och grupper**.
   - Klicka på **Välj** om du vill välja en grupp eller uppsättning användare som ska påverkas av MFA.
   - Klicka på **Klar**.

   ![Tilldelningar](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Navigera till **access-kontrollerna -> bidragsfönstret** i fönstret **Nytt:**

   - Klicka på **Bevilja åtkomst**.
   - Klicka på **Kräv multifaktorautentisering**.
   - Klicka på **Kräv alla markerade kontroller**.
   - Klicka på **Markera**.
   
   ![Tillgång till bidrag - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. I avsnittet **Aktivera princip:**

   - Välj **På**.
   - Klicka på **Skapa**.

   ![Aktivera princip](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)