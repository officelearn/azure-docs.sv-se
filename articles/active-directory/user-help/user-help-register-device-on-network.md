---
title: Registrera personliga enheter i en organisations nätverk - Azure AD
description: Läs om hur du registrerar din personliga enhet i organisationens nätverk så att du kan komma åt organisationens skyddade resurser.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e2e6585bac100a09f3f98037e90b24738e22816f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063874"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrera din personliga enhet i organisationens nätverk
Registrera din personliga enhet (vanligtvis en telefon eller surfplatta) i organisationens nätverk. När enheten har registrerats kan den komma åt organisationens begränsade resurser.

>[!Note]
>Den här artikeln använder en Windows-enhet i demonstrationssyfte, men du kan också registrera enheter som kör iOS, Android eller macOS.

## <a name="what-happens-when-you-register-your-device"></a>Vad händer när du registrerar enheten
När du registrerar enheten i organisationens nätverk kommer följande åtgärder att vidtas:

- Windows registrerar enheten i organisationens nätverk.

- Alternativt, baserat på organisationens val, kan du bli ombedd att konfigurera tvåstegsverifiering via antingen [multifaktorautentisering](multi-factor-authentication-end-user-first-time.md) eller [säkerhetsinformation](user-help-security-info-overview.md).

- Alternativt, baserat på organisationens val, kan du automatiskt registreras i hantering av mobila enheter, till exempel Microsoft Intune. Mer information om hur du registrerar dig i Microsoft Intune finns [i Registrera din enhet i Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Du går igenom inloggningsprocessen med användarnamn och lösenord för ditt arbets- eller skolkonto.

## <a name="to-register-your-windows-device"></a>Så här registrerar du din Windows-enhet

Följ dessa steg för att registrera din personliga enhet i nätverket.

1. Öppna **Inställningar**och välj sedan **Konton**.

    ![Konton på skärmen Inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **Access-arbete eller skola**och välj sedan **Anslut** på **skärmen Access-arbete eller skola.**

    ![Komma åt arbets- eller skolskärmen med alternativet Anslut markerat](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. På skärmen **Lägg till ett arbets- eller skolkonto** skriver du in din e-postadress för ditt arbets- eller skolkonto och väljer sedan **Nästa**. Till exempel alain@contoso.com.

4. Logga in på ditt arbets- eller skolkonto och välj sedan **Logga in**.

5. Slutför resten av registreringsprocessen, inklusive godkännande av din begäran om identitetsverifiering (om du använder tvåstegsverifiering) och konfigurera Windows Hello (om det behövs).

## <a name="to-verify-that-youre-registered"></a>Så här verifierar du att du är registrerad
Du kan kontrollera att du är registrerad genom att titta på dina inställningar.

1. Öppna **Inställningar**och välj sedan **Konton**.

    ![Konton på skärmen Inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **Åtkomst till arbete eller skola**och se till att du ser ditt arbets- eller skolkonto.

    ![Komma åt arbets- eller skolskärm med anslutet contoso-konto](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Nästa steg
När du har registrerat din personliga enhet i organisationens nätverk bör du kunna komma åt de flesta av dina resurser.

- Om din organisation vill att du ska gå med i din arbetsenhet läser [du Ansluta till din arbetsenhet till organisationens nätverk](user-help-join-device-on-network.md).



