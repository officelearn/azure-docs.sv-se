---
title: "Azure AD: Återställa ditt lösenord | Microsoft Docs"
description: "Använda Självbetjäning för återställning av lösenord för att få åtkomst till ditt arbete eller skola användarkonto"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 89ddbaff88584d83a109b68707f9a9b3accb8e70
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="reset-your-work-or-school-password"></a>Återställa ditt lösenord för arbetet eller skolan

Om du har glömt ditt lösenord, aldrig fått ett från ditt företags-stöd, har låsts ut från ditt konto eller ändra det, kan vi hjälpa. Om du känner till ditt lösenord och behöver bara ändra det fortfarande den [ändra lösenordet](#change-my-password) avsnitt.

   > [!NOTE]
   > Om du försöker få tillbaka ditt eget konto som Xbox, hotmail.com eller outlook.com provar i den [när du inte logga in till ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikel.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Återställa eller låsa upp lösenordet för ett arbets- eller skolkonto

Du kanske inte kan komma åt ditt konto för Azure Active Directory (AD Azure) på grund av något av följande skäl:

* Ditt lösenord fungerar inte och du vill återställa den.
* Du känner till lösenordet, men ditt konto är låst och du vill låsa upp den.

Använd följande steg för att komma åt Azure AD lösenordsåterställning via självbetjäning (SSPR) och få tillbaka ditt konto.

1. Från vilken som helst arbetsplats eller skola **inloggning** väljer den **kan inte komma åt ditt konto?** länka och välj sedan **fungerar- eller skolkonto** eller gå direkt till den [ Sidan för lösenordsåterställning](https://passwordreset.microsoftonline.com/).

    ![Can't access your account?][Login] (Kan du inte komma åt ditt konto?)

2. Ange ditt arbete eller skola **användar-ID**, bevisa att du inte är en robot genom att ange de tecken du ser på skärmen och välj sedan **nästa**.

   > [!NOTE]
   > Om din IT-personal inte har aktiverat den här funktionen visas en ”kontaktar du administratören” länk så att din IT-personal kan hjälpa via e-post eller en webbportal sina egna.
   >
   > Om du behöver låsa upp ditt konto nu välja alternativet **jag vet mitt lösenord, men ändå inte logga in.**
   >

3. Beroende på hur IT-personalen har konfigurerat SSPR bör du se en eller flera av följande autentiseringsmetoder. Du eller din IT-personal bör ifyllda del av den här informationen genom att följa stegen i den [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md) artikel.

   * **Skicka e-post till min alternativa e-postadress**
   * **Skicka ett sms till min mobiltelefon**
   * **Ring min mobiltelefon**
   * **Ring min arbetstelefon**
   * **Svara på mina säkerhetsfrågor**

   Välj ett alternativ, ange rätt svar och välj sedan **nästa**.

   ![Kontrollera dina autentiseringsdata][Verification]

4. Din IT-personal kan behöva mer kontroll och du kan behöva upprepa steg 3 med ett annat alternativ.
5. På den **välja ett nytt lösenord** , ange ett nytt lösenord, bekräfta lösenordet och sedan väljer **Slutför**. Ditt lösenord för arbetet eller skolan kanske vissa krav som du måste följa. Vi rekommenderar att du som välja ett lösenord som är 8-16 tecken och innehåller gemener och versaler, en siffra och ett specialtecken.
6. När du ser meddelandet **ditt lösenord har återställts**, du kan logga in med ditt nya lösenord.

    ![Ditt lösenord har återställts][Complete]

Du bör nu kunna komma åt ditt konto. Om du inte kommer åt ditt konto, bör du kontakta din organisations IT-avdelning om du behöver hjälp.

Du kan få ett e-postbekräftelse som kommer från ett konto som ”Microsoft på uppdrag av \<organisationen >”. Om du får ett e-postmeddelande som det här och du inte använde Självbetjäning för återställning av lösenord för att få åtkomst till ditt konto, kontakta din organisations IT-personal.

## <a name="change-my-password"></a>Ändra mitt lösenord

Om du redan känner till ditt lösenord och vill ändra det, Använd följande steg.

### <a name="change-your-password-from-the-office-365-portal"></a>Ändra ditt lösenord från Office 365-portalen

Använd den här metoden om du normalt kommer åt dina program via Office-portalen:

1. Logga in på ditt [Office 365-konto](https://www.office.com) med ditt befintliga lösenord.
2. Välj din profil på upp till höger och välj sedan **konto**.
3. Välj **säkerhet och sekretess** > **lösenord**.
4. Ange ditt gamla lösenord, ange och bekräfta det nya lösenordet och välj sedan **skicka**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Ändra ditt lösenord från Azures åtkomstpanel

Använd den här metoden om du normalt åtkomst till dina program från Azure-åtkomstpanelen (MyApps):

1. Logga in på den [Azure åtkomstpanelen](https://myapps.microsoft.com/) med ditt befintliga lösenord.
2. Välj din profil på upp till höger och välj sedan **profil**.
3. Välj **ändra lösenord**.
4. Ange ditt gamla lösenord, ange och bekräfta det nya lösenordet och välj sedan **skicka**.

## <a name="reset-password-at-sign-in"></a>Återställ lösenord vid inloggning

Om administratören har aktiverat funktionen, kan du nu se en länk till **Återställ lösenord** på skärmen för Windows 10 faller skapare Update.

![Inloggningssida][LoginScreen]

Välj den **Återställ lösenord** länk för att öppna in SSPR-upplevelse vid skärmen inloggning så att du kan återställa ditt lösenord utan att behöva logga in att komma åt den normala webbaserad upplevelsen.

1. Bekräfta ditt användar-ID och välj **nästa**.
2. Välj och bekräfta en kontaktmetod för verifiering. Din IT-personal kan behöva mer kontroll och du kan behöva upprepa det här steget med ett annat alternativ.

   ![Kontaktmetod][ContactMethod]

3. På den **skapa ett nytt lösenord** , ange ett nytt lösenord, bekräfta lösenordet och sedan väljer **nästa**. Vi rekommenderar att lösenordet är 8-16 tecken långt och består av versaler och gemener, siffror och specialtecken.

   ![Återställ lösenord][ResetPassword]

4. När du ser meddelandet **ditt lösenord har återställts**väljer **Slutför**.

Du bör nu kunna komma åt ditt konto. Om inte, kontakta din organisations IT-avdelning om du behöver hjälp.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer tillfällen vanliga fel och lösningar:

| Fel-fall| Vilka fel ser du?| Lösning |
| --- | --- | --- |
| Det finns ett fel när jag försöker ändra lösenordet. | Lösenordet innehåller tyvärr ett ord, en fras eller en mönster som gör att enkelt fjärråtkomstlösning ditt lösenord. Försök igen med ett annat lösenord. | Välj ett lösenord som är mer difficlt att gissa. |
| En ”Kontakta din administratör” sida kommer när du har angett mitt användar-ID | Kontakta administratören. <br> <br> Vi har upptäckt att lösenordet till ditt användarkonto inte hanteras av Microsoft. Vi kan därför inte att återställa lösenordet automatiskt. <br> <br> Du måste kontakta din IT-personal för ytterligare hjälp. | Du se detta meddelande eftersom din IT-personal hanterar lösenordet i din lokala miljö. Du kan inte återställa ditt lösenord från länken ”det går inte att komma åt ditt konto”. <br> <br> Kontakta din IT-personal direkt för att få hjälp om du vill återställa ditt lösenord och meddela dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig.|
| Jag felmeddelandet ”ditt konto har inte aktiverats för lösenordsåterställning” när du har angett mitt användar-ID | Ditt konto har inte aktiverats för återställning av lösenord. <br> <br> Vi beklagar, men din IT-personal inte har lagt upp ditt konto att använda tjänsten. <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du se detta meddelande eftersom din IT-personal inte har aktiverats för din organisation från ”det går inte att komma åt ditt konto” länken för återställning av lösenord eller har inte licensierats du om du vill använda funktionen. <br> <br> Välj om du vill återställa ditt lösenord ”kontakta en administratör länken” att skicka ett e-postmeddelande till ditt företag har IT-personal och meddela dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig. |
| Jag felmeddelandet ”Det gick inte att verifiera ditt konto” när du har angett mitt användar-ID | Det gick inte att verifiera ditt konto. <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du se detta meddelande eftersom du har aktiverat för återställning av lösenord, men du har inte registrerats för att använda tjänsten. Registrera för återställning av lösenord genom att gå till http://aka.ms/ssprsetup när du har återupprättats åtkomst till ditt konto. <br> <br> Om du vill återställa ditt lösenord, Välj länken ”kontakta en administratör” om du vill skicka ett e-postmeddelande till ditt företags IT-personal. |

## <a name="next-steps"></a>Nästa steg

* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md)
* [Registreringssida för lösenordsåterställning](http://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)
* [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Inloggningssida – Kan du inte komma åt ditt konto?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Kontrollera dina autentiseringsdata"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Ändra ditt lösenord"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Ditt lösenord har återställts"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Länken för Windows 10 faller skapare Update inloggningen skärmen återställning av lösenord"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Kontrollera dina autentiseringsdata"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Ändra ditt lösenord"

