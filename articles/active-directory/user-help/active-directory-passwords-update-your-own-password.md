---
title: Återställa ditt lösenord – Azure Active Directory | Microsoft Docs
description: Använda Självbetjäning för återställning av lösenord för att få åtkomst till ditt arbets- eller skolkonto för användare
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: b4e784e7d249976b95c5f70986ba007351d2abb4
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076984"
---
# <a name="reset-your-work-or-school-password"></a>Återställa lösenordet för arbets- eller skolkonto

Om du har glömt ditt lösenord, aldrig fått ett från företagets support, har blivit utelåst från ditt konto eller om du vill ändra den, kan vi hjälpa. Om du vet ditt lösenord och behöver bara ändra det fortfarande den [ändra mitt lösenord](#change-my-password) avsnittet.

   > [!NOTE]
   > Om du försöker få tillbaka ditt personliga konto som Xbox, hotmail.com eller outlook.com provar i den [när du inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Återställa eller låsa upp lösenordet för ett arbets- eller skolkonto

Du kanske inte kan komma åt ditt konto för Azure Active Directory (Azure AD) på grund av något av följande orsaker:

* Ditt lösenord fungerar inte och du vill återställa den.
* Du vet ditt lösenord, men ditt konto är låst och du vill låsa upp den.

Använd följande steg för att få åtkomst till Azure AD för återställning av lösenord via självbetjäning (SSPR) och få tillbaka ditt konto.

1. Från en arbetsplats eller skola **inloggning** väljer den **kan inte komma åt ditt konto?** länka och välj sedan **fungerar- eller skolkonto** eller gå direkt till den [ Sidan för lösenordsåterställning](https://passwordreset.microsoftonline.com/).

    ![Can't access your account?][Login] (Kan du inte komma åt ditt konto?)

2. Ange ditt arbete eller din skola **användar-ID**, bevisa att du inte är en robot genom att ange de tecken du visas på skärmen och välj sedan **nästa**.

   > [!NOTE]
   > Om din IT-avdelningen inte har aktiverat den här funktionen visas en länk för ”Kontakta administratören” så att IT-personalen kan hjälpa att via e-post eller en webbportal sina egna.
   >
   > Om du vill låsa upp kontot kan nu välja alternativet **jag vet mitt lösenord, men ändå inte logga in.**
   >

3. Beroende på hur din IT-avdelning har konfigurerat Lösenordsåterställningen visas ett eller flera av följande autentiseringsmetoder. Du eller din IT-personal bör måste fylla i delar av den här informationen genom att följa stegen i den [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md) artikeln.

   * **Skicka e-post till min alternativa e-postadress**
   * **Skicka ett sms till min mobiltelefon**
   * **Ring min mobiltelefon**
   * **Ring min arbetstelefon**
   * **Svara på mina säkerhetsfrågor**

   Välj ett alternativ, ange rätt svar och välj sedan **nästa**.

   ![Kontrollera dina autentiseringsdata][Verification]

4. Din IT-personal kanske kräver ytterligare verifiering och du kan behöva upprepa steg 3 med ett annat alternativ.
5. På den **välja ett nytt lösenord** sidan, ange ett nytt lösenord, bekräftar ditt lösenord och välj sedan **Slutför**. Lösenordet för arbets- eller skolkonto kan ha vissa krav som du måste följa. Vi rekommenderar att du som välja ett lösenord som är 8 och 16 tecken långt och som innehåller gemener och versaler, en siffra och ett specialtecken.
6. När du ser meddelandet **ditt lösenord har återställts**, du kan logga in med ditt nya lösenord.

    ![Ditt lösenord har återställts][Complete]

Du bör nu kunna komma åt ditt konto. Om du inte komma åt ditt konto, bör du kontakta din organisations IT-avdelning om du behöver hjälp.

Du kan få en bekräftelse via e-post från ett konto som ”Microsoft på uppdrag av \<organisationen >”. Om du får ett e-postmeddelande som det här och du inte använde Självbetjäning för återställning av lösenord för att få åtkomst till ditt konto, kontakta din organisations IT-personal.

## <a name="change-my-password"></a>Ändra mitt lösenord

Använd följande steg om du redan vet ditt lösenord och vill ändra den.

### <a name="change-your-password-from-the-office-365-portal"></a>Ändra ditt lösenord från Office 365-portalen

Använd den här metoden om du normalt kommer åt dina program via Office-portalen:

1. Logga in på din [Office 365-konto](https://www.office.com) med ditt befintliga lösenord.
2. Välj din profil längst upp till höger och välj sedan **visa konto**.
3. Välj **säkerhet och sekretess** > **lösenord**.
4. Ange ditt gamla lösenord, ange och bekräfta det nya lösenordet och välj sedan **skicka**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Ändra ditt lösenord från Azures åtkomstpanel

Använd den här metoden om du normalt kommer åt dina program från Azures åtkomstpanel (MyApps):

1. Logga in på den [Azures åtkomstpanel](https://myapps.microsoft.com/) med ditt befintliga lösenord.
2. Välj din profil längst upp till höger och välj sedan **profil**.
3. Välj **ändra lösenord**.
4. Ange ditt gamla lösenord, ange och bekräfta det nya lösenordet och välj sedan **skicka**.

## <a name="reset-password-at-sign-in"></a>Återställ lösenord vid inloggning

Om administratören har aktiverat funktionen, kan du nu se en länk till **Återställ lösenord** på skärmen för Windows 10 Fall Creators Update.

![Inloggningsskärm][LoginScreen]

Välj den **Återställ lösenord** länk för att öppna SSPR-funktion på skärmen inloggning så att du kan återställa ditt lösenord utan att behöva logga in att få åtkomst till den normala webbaserad upplevelsen.

1. Bekräfta ditt användar-ID och välj **nästa**.
2. Välj och bekräfta en kontaktmetod för verifiering. Din IT-personal kanske kräver ytterligare verifiering och du kan behöva upprepa det här steget med ett annat alternativ.

   ![Kontaktmetod][ContactMethod]

3. På den **skapa ett nytt lösenord** sidan, ange ett nytt lösenord, bekräftar ditt lösenord och välj sedan **nästa**. Vi rekommenderar att lösenordet är 8-16 tecken långt och består av versaler och gemener, siffror och specialtecken.

   ![Återställ lösenord][ResetPassword]

4. När du ser meddelandet **ditt lösenord har återställts**väljer **Slutför**.

Du bör nu kunna komma åt ditt konto. Om inte, kontakta din organisations IT-avdelning om du behöver hjälp.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer några vanliga fel och sina lösningar:

| Felsituation| Vilka fel ser du?| Lösning |
| --- | --- | --- |
| Jag ser ett fel när jag försöker ändra mitt lösenord. | Tyvärr innehåller ditt lösenord ett ord, en fras eller ett mönster som gör det enkelt att gissa ditt lösenord. Försök igen med ett annat lösenord. | Välj ett lösenord som är svårare att gissa. |
| Jag får en ”Kontakta administratören”-sida när du har angett mitt användar-ID | Kontakta administratören. <br> <br> Vi har upptäckt att lösenordet till ditt användarkonto inte hanteras av Microsoft. Vi kan därför inte att automatiskt återställa ditt lösenord. <br> <br> Du måste kontakta IT-avdelningen för mer hjälp. | Du får det här meddelandet eftersom din IT-personal hanterar ditt lösenord i din lokala miljö. Du kan inte återställa ditt lösenord från länken ”det går inte att komma åt ditt konto”. <br> <br> Kontakta din IT-personal direkt för att få hjälp om du vill återställa ditt lösenord och informera dem om du vill återställa lösenordet så att de kan aktivera den här funktionen för dig.|
| Jag får felet ”ditt konto har inte aktiverats för återställning av lösenord” när du har angett mitt användar-ID | Ditt konto har inte aktiverats för återställning av lösenord. <br> <br> Vi beklagar, men IT-avdelningen har inte konfigurerat ditt konto att använda tjänsten. <br> <br> Om du vill kan kontakta vi en administratör i din organisation som återställer lösenordet åt dig. | Du får det här meddelandet eftersom din IT-personal inte har aktiverat återställning av lösenord för din organisation från länken ”det går inte att komma åt ditt konto” eller har inte licensierats du om du vill använda funktionen. <br> <br> Om du vill återställa ditt lösenord, väljer du ”kontakta en administratör länken” att skicka ett e-postmeddelande till ditt företag är IT-personal och informera dem om du vill återställa lösenordet så att de kan aktivera den här funktionen för dig. |
| Jag får felet ”Det gick inte att verifiera ditt konto” när du har angett mitt användar-ID | Det gick inte att verifiera ditt konto. <br> <br> Om du vill kan kontakta vi en administratör i din organisation som återställer lösenordet åt dig. | Du får det här meddelandet eftersom du har aktiverats för återställning av lösenord, men du har inte registrerats för användning av tjänsten. Registrera för återställning av lösenord genom att gå till https://aka.ms/ssprsetup när du har återupprättats åtkomst till ditt konto. <br> <br> Om du vill återställa ditt lösenord, väljer du länken ”kontakta en administratör” för att skicka ett e-postmeddelande till ditt företags IT-personal. |

## <a name="next-steps"></a>Nästa steg

* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md)
* [Registreringssida för lösenordsåterställning](https://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)
* [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Inloggningssida – Kan du inte komma åt ditt konto?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Kontrollera dina autentiseringsdata"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Ändra ditt lösenord"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Ditt lösenord har återställts"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Länken för Windows 10 Fall Creators Update inloggningen skärmen Återställ lösenord"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Kontrollera dina autentiseringsdata"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Ändra ditt lösenord"
