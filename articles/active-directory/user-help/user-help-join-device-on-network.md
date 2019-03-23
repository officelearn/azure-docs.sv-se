---
title: Anslut din enhet för arbete till din organisations nätverk – Azure Active Directory | Microsoft Docs
description: Lär dig hur du kopplar work-enhet till din organisations nätverk.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2ba6b2d33c3fb5d9fda6821718ac61513a958b7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369147"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Anslut din enhet för arbete till din organisations nätverk
Anslut Windows 10-enheten ägs av arbetet till din organisations nätverk så att du kan komma åt potentiellt begränsade resurser.

## <a name="what-happens-when-you-join-your-device"></a>Vad händer när du ansluter din enhet
När du kopplar din Windows 10-enhet till din organisations nätverk, sker följande åtgärder:

- Windows registrerar din enhet till din organisations nätverk, så att du kan komma åt dina resurser med ditt personliga konto. När enheten har registrerats kan kopplar Windows sedan enheten till nätverket, så du kan använda din organisations användarnamn och lösenord för att logga in och komma åt skyddade resurser.

- Du kan också baserat på din organisations alternativ, du kan bli ombedd att ställa in tvåstegsverifiering via antingen [Multifaktorautentisering](multi-factor-authentication-end-user-first-time.md) eller [säkerhetsinformation](user-help-security-info-overview.md).

- Du kan också kan baserat på din organisations alternativ, du automatiskt registreras i hantering av mobila enheter, t.ex Microsoft Intune. Mer information om hur du registrerar i Microsoft Intune finns i [registrera din enhet i Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Du ska gå igenom processen logga in med hjälp av automatisk att logga in med ditt organisationskonto.

## <a name="to-join-a-brand-new-windows-10-device"></a>Att ansluta till en helt ny Windows 10-enhet
Om enheten är helt ny och har inte ställts in ännu kan gå du igenom Windows Out of Box Experience (OOBE) processen för att ansluta enheten till nätverket.

1. Starta den nya enheten och starta OOBE-processen.

2. På den **logga in med Microsoft** skärmen, skriver du ditt arbete eller skolans e-postadress.

    ![Inloggningsskärmen med e-postadress](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. På den **ange ditt lösenord** skärmen, Skriv ditt lösenord.

    ![Ange ditt lösenord-skärmen](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Godkänn din enhet på din mobila enhet, så att den kan komma åt ditt konto. 

    ![SMS-skärmen](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Slutför OOBE-processen, inklusive att dina sekretessinställningar och konfigurera Windows Hello (vid behov).

    Enheten är nu ansluten till nätverket.

## <a name="to-make-sure-youre-joined"></a>Om du vill kontrollera att är du ansluten
Du kan se till att du är ansluten genom att titta på dina inställningar.

1. Öppna **inställningar**, och välj sedan **konton**.

    ![Konton på skärmen inställningar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola**, och se till att du ser texten som säger något som, **är anslutna till *< your_organization >* Azure AD**.

    ![Få åtkomst till arbets- eller skolkonto skärmen med anslutna contoso-konto](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Att ansluta till en redan konfigurerade Windows 10-enhet
Om du har haft din enhet för ett tag och den har redan ställts in kan du följa dessa steg för att ansluta enheten till nätverket.

1. Öppna **inställningar**, och välj sedan **konton**.

2. Välj **åtkomst till arbete eller skola**, och välj sedan **Connect**.

    ![Åtkomst till arbete eller skola och Anslut länkar](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. På den **upprätta ett arbets- eller skolkonto konto** väljer **Anslut den här enheten till Azure Active Directory**.

    ![Konfigurera ett arbets- eller skolkonto konto skärmen](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. På den **registrera dig** skärmen, Skriv din e-postadress (till exempel alain@contoso.com), och välj sedan **nästa**.

    ![Registrera dig i skärmen](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. På den **ange lösenord** skärmen, ange ditt lösenord och välj sedan **logga in**.

    ![Ange lösenord](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Godkänn din enhet på din mobila enhet, så att den kan komma åt ditt konto. 

    ![SMS-skärmen](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. På den **se till att det här är din organisation** skärmen, granskar du informationen för att kontrollera att det är rätt och välj sedan **ansluta**.

    ![Kontrollera att det här är din organisation verifiering skärm](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. På den **allt är klart** klickar du på **klar**.

    ![Du är alla set-skärmen](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Om du vill kontrollera att är du ansluten
Du kan se till att du är ansluten genom att titta på dina inställningar.

1. Öppna **inställningar**, och välj sedan **konton**.

    ![Konton på skärmen inställningar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Välj **åtkomst till arbete eller skola**, och se till att du ser texten som säger något som, **är anslutna till *< your_organization >* Azure AD**.

    ![Få åtkomst till arbets- eller skolkonto skärmen med anslutna contoso-konto](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Nästa steg
När du ansluter din enhet till din organisations nätverk bör du kunna komma åt alla dina resurser med hjälp av ditt arbete eller skola kontoinformation.

- Om du registrerar en enhet, till exempel din telefon företaget Se [registrera en enhet på din organisations nätverk](user-help-register-device-on-network.md).

