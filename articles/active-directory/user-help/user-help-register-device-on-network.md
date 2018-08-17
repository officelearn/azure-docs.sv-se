---
title: Registrera en enhet på din organisations nätverk – Azure Active Directory | Microsoft Docs
description: Lär dig hur du registrerar en enhet på din organisations nätverk så att du kan komma åt skyddade resurser i din organisation.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180927"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrera en enhet på din organisations nätverk
Registrera en enhet, vanligtvis en telefon eller surfplatta, på din organisations nätverk. När enheten har registrerats, kommer det att kunna komma åt skyddade resurser i din organisation.

>[!Note]
>Den här artikeln använder en Windows-enhet i demonstrationssyfte, men du kan också registrera enheter som kör iOS, Android- eller macOS.

## <a name="what-happens-when-you-register-your-device"></a>Vad händer när du registrerar din enhet
När du registrerat enheten i din organisations nätverk kan sker följande åtgärder:

- Windows registrerar enheten i din organisations nätverk.

- Du kan också baserat på din organisations alternativ, du kan bli ombedd att ställa in tvåstegsverifiering via antingen [Multifaktorautentisering](multi-factor-authentication-end-user-first-time.md) eller [säkerhetsinformation](user-help-security-info-overview.md).

- Du kan också kan baserat på din organisations alternativ, du automatiskt registreras i hantering av mobila enheter, t.ex Microsoft Intune. Mer information om hur du registrerar i Microsoft Intune finns i [registrera din enhet i Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Du ska gå igenom processen logga in med användarnamnet och lösenordet för ditt personliga Microsoft-konto.

## <a name="to-register-your-windows-device"></a>Registrera din Windows-enhet

Följ dessa steg för att registrera en enhet i nätverket.

1. Öppna **inställningar**, och välj sedan **konton**.

    ![Konton på skärmen inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **e-post och konton**, och välj sedan **ansluter till en Microsoft-kontot**.

    ![E-post & konton och Lägg till en Microsoft-konto länkar](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. På den **lägger du till ditt Microsoft-konto** skärmen, ange din e-postadress för ditt personliga Microsoft-konto.

    ![Lägg till skärmen Microsoft-konto med e-postadress](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. På den **ange lösenord** skärmen, skriver du lösenordet för ditt personliga Microsoft-konto och välj sedan **logga in**.

    ![Ange lösenord för skärmen](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. Slutför resten av registreringen, inklusive godkänna din begäran om identitetsverifiering (om du använder tvåstegsverifiering) och konfigurera Windows Hello (vid behov).

## <a name="to-make-sure-youre-registered"></a>Om du vill kontrollera att registreras du
Du kan se till att du har registrerat genom att titta på dina inställningar.

1. Öppna **inställningar**, och välj sedan **konton**.

    ![Konton på skärmen inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **e-post och konton**, och se till att du ser ditt personliga Microsoft-konto.

    ![Få åtkomst till arbets- eller skolkonto skärmen med anslutna contoso-konto](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>Nästa steg
När du har registrerat din personliga enhet till din organisations nätverk bör du kunna komma åt de flesta av dina resurser.

- Om din organisation vill du att ansluta din enhet för arbete, se [ansluter din work-enhet till din organisations nätverk](user-help-join-device-on-network.md).



