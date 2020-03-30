---
title: Gå med i din arbetsenhet i organisationens nätverk - AD
description: Läs om hur du ansluter din arbetsenhet till organisationens nätverk.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 0ff8b85a15d94ded2d702e0df247f9ebc4d3f923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266317"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Ansluta till din arbetsenhet i organisationens nätverk
Gå med i din arbetsägda Windows 10-enhet i organisationens nätverk så att du kan komma åt potentiellt begränsade resurser.

## <a name="what-happens-when-you-join-your-device"></a>Vad händer när du ansluter till enheten
När du ansluter din Windows 10-enhet till organisationens nätverk kommer följande åtgärder att utföras:

- Windows registrerar enheten i organisationens nätverk så att du kan komma åt dina resurser med ditt personliga konto. När enheten har registrerats ansluter Windows sedan enheten till nätverket, så att du kan använda organisationens användarnamn och lösenord för att logga in och komma åt begränsade resurser.

- Alternativt, baserat på organisationens val, kan du bli ombedd att konfigurera tvåstegsverifiering via antingen [multifaktorautentisering](multi-factor-authentication-end-user-first-time.md) eller [säkerhetsinformation](user-help-security-info-overview.md).

- Alternativt, baserat på organisationens val, kan du automatiskt registreras i hantering av mobila enheter, till exempel Microsoft Intune. Mer information om hur du registrerar dig i Microsoft Intune finns [i Registrera din enhet i Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Du går igenom inloggningsprocessen med hjälp av automatisk inloggning med ditt organisationskonto.

## <a name="to-join-a-brand-new-windows-10-device"></a>Så här ansluter du till en helt ny Windows 10-enhet
Om enheten är helt ny och inte har konfigurerats ännu kan du gå igenom OOBE-processen (Windows Out of Box Experience) för att ansluta enheten till nätverket.

1. Starta din nya enhet och påbörja OOBE-processen.

2. Skriv din e-postadress för arbete eller skola på skärmen **Logga in med Microsoft.**

    ![Logga in på skärmen med e-postadress](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Skriv lösenordet på skärmen **Ange lösenordet.**

    ![Ange lösenordsskärmen](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. På din mobila enhet godkänner du enheten så att den kan komma åt ditt konto. 

    ![Skärmen Mobilmeddelande](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Slutför OOBE-processen, inklusive att ange dina sekretessinställningar och konfigurera Windows Hello (om det behövs).

    Enheten är nu ansluten till organisationens nätverk.

## <a name="to-make-sure-youre-joined"></a>Så här kontrollerar du att du är ansluten
Du kan se till att du får sällskap genom att titta på dina inställningar.

1. Öppna **Inställningar**och välj sedan **Konton**.

    ![Konton på skärmen Inställningar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Välj **Access-arbete eller skola**och se till att du ser text som säger något i stil med **Ansluten till * \<your_organization>* Azure AD**.

    ![Komma åt arbets- eller skolskärm med anslutet contoso-konto](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Så här ansluter du till en redan konfigurerad Windows 10-enhet
Om du har haft enheten ett tag och den redan har konfigurerats kan du följa dessa steg för att ansluta enheten till nätverket.

1. Öppna **Inställningar**och välj sedan **Konton**.

2. Välj **Access-arbete eller skola**och välj sedan **Anslut**.

    ![Få tillgång till länkar till arbete eller skola och Anslut](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. På skärmen **Konfigurera ett arbets- eller skolkonto** väljer du **Anslut den här enheten till Azure Active Directory**.

    ![Konfigurera en skärm för arbets- eller skolkonto](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Skriv **Let's get you signed in** din e-postadress (till exempel alain@contoso.com) på skärmen Låt dig logga in och välj sedan **Nästa**.

    ![Låt oss få dig inloggad skärm](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Skriv lösenordet på skärmen **Ange lösenord** och välj sedan **Logga in**.

    ![Ange lösenord](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. På din mobila enhet godkänner du enheten så att den kan komma åt ditt konto. 

    ![Skärmen Mobilmeddelande](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. På skärmen **Kontrollera att det här är din organisation** granskar du informationen för att kontrollera att den är rätt och väljer sedan Gå **med**.

    ![Kontrollera att det här är din organisationsverifieringsskärm](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Klicka på **Klar**på skärmen **Du är klar.**

    ![Du är helt inställd skärm](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Så här kontrollerar du att du är ansluten
Du kan se till att du får sällskap genom att titta på dina inställningar.

1. Öppna **Inställningar**och välj sedan **Konton**.

    ![Konton på skärmen Inställningar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Välj **Access-arbete eller skola**och se till att du ser text som säger något i stil med **Ansluten till * \<your_organization>* Azure AD**.

    ![Komma åt arbets- eller skolskärm med anslutet contoso-konto](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Nästa steg
När du har anslutat enheten till organisationens nätverk bör du kunna komma åt alla dina resurser med hjälp av din arbets- eller skolkontoinformation.

- Om din organisation vill att du ska registrera din personliga enhet, till exempel telefonen, läser [du Registrera din personliga enhet i organisationens nätverk](user-help-register-device-on-network.md).

- Om din organisation hanteras med Microsoft Intune och du har frågor om registrering, inloggning eller andra Intune-relaterade problem läser du [Intune-användarhjälpsinnehållet](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done).