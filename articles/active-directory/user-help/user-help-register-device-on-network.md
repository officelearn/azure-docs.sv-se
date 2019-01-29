---
title: Registrera en enhet på din organisations nätverk – Azure Active Directory | Microsoft Docs
description: Lär dig hur du registrerar en enhet på din organisations nätverk så att du kan komma åt skyddade resurser i din organisation.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: a659b4d25581153228cff6102b2835ef2e012981
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154787"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrera en enhet på din organisations nätverk
Registrera din personliga enhet (vanligtvis en telefon eller surfplatta) på din organisations nätverk. När enheten har registrerats, kommer det att kunna komma åt skyddade resurser i din organisation.

>[!Note]
>Den här artikeln använder en Windows-enhet i demonstrationssyfte, men du kan också registrera enheter som kör iOS, Android- eller macOS.

## <a name="what-happens-when-you-register-your-device"></a>Vad händer när du registrerar din enhet
När du registrerat enheten i din organisations nätverk kan sker följande åtgärder:

- Windows registrerar enheten i din organisations nätverk.

- Du kan också baserat på din organisations alternativ, du kan bli ombedd att ställa in tvåstegsverifiering via antingen [Multifaktorautentisering](multi-factor-authentication-end-user-first-time.md) eller [säkerhetsinformation](user-help-security-info-overview.md).

- Du kan också kan baserat på din organisations alternativ, du automatiskt registreras i hantering av mobila enheter, t.ex Microsoft Intune. Mer information om hur du registrerar i Microsoft Intune finns i [registrera din enhet i Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Du ska gå igenom processen logga in med användarnamnet och lösenordet för arbets-eller skolkonto.

## <a name="to-register-your-windows-device"></a>Registrera din Windows-enhet

Följ dessa steg för att registrera en enhet i nätverket.

1. Öppna **inställningar**, och välj sedan **konton**.

    ![Konton på skärmen inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola**, och välj sedan **Connect** från den **åtkomst till arbete eller skola** skärmen.

    ![Få åtkomst till arbets- eller skolkonto skärmen med alternativet Anslut markerad](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. På den **Lägg till ett arbets- eller skolkonto** skärmen, Skriv din e-postadress för ditt arbets- eller skolkonto konto och välj sedan **nästa**. Till exempel alain@contoso.com.

4. Logga in på ditt arbets- eller skolkonto konto och välj sedan **logga in**.

5. Slutför resten av registreringen, inklusive godkänna din begäran om identitetsverifiering (om du använder tvåstegsverifiering) och konfigurera Windows Hello (vid behov).

## <a name="to-verify-that-youre-registered"></a>Kontrollera att du har registrerat
Du kan se till att du har registrerat genom att titta på dina inställningar.

1. Öppna **inställningar**, och välj sedan **konton**.

    ![Konton på skärmen inställningar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola**, och se till att du ser ditt arbets- eller skolkonto.

    ![Få åtkomst till arbets- eller skolkonto skärmen med anslutna contoso-konto](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Nästa steg
När du har registrerat din personliga enhet till din organisations nätverk bör du kunna komma åt de flesta av dina resurser.

- Om din organisation vill du att ansluta din enhet för arbete, se [ansluter din work-enhet till din organisations nätverk](user-help-join-device-on-network.md).



