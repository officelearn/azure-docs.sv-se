---
title: "Azure AD: Återställa ditt lösenord | Microsoft Docs"
description: "Använda Självbetjäning för återställning av lösenord för att få åtkomst till ditt arbete eller din skola användarkonto"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 763248b1b7adf821d37f2308195a0934a0aee152
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="reset-your-work-or-school-password"></a>Återställa ditt lösenord för arbetet eller skolan

Om du har glömt ditt lösenord aldrig något från ditt företags-stöd, låsts utanför ditt konto, eller om du vill ändra det, kan vi hjälpa. Om du känner till ditt lösenord och bara behöver ändra den fortsätta till avsnittet [ändra lösenordet](#change-my-password) nedan.

   > [!NOTE]
   > Om du försöker komma åt din personliga konto som outlook.com, Xbox eller hotmail.com försök den [förslag hittades i den här artikeln](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Återställa eller låsa upp lösenordet för ett arbets- eller skolkonto

Om du inte kan komma åt ditt konto på grund av något av följande:

* Ditt lösenord fungerar inte och du vill återställa den.
* Du känner till lösenordet, men ditt konto är låst och du vill låsa upp kontot.

Följ instruktionerna som följer för att få åtkomst till Azure AD lösenordsåterställning via självbetjäning, SSPR som vi vill att anropa den och få tillgång till ditt konto.

1. Klicka på länken **Can't access your account?** (Kan du inte komma åt ditt konto?) på inloggningssidan för ditt arbete eller din skola och klicka sedan på **Work or school account** (Arbets- eller skolkonto) eller gå direkt till [sidan för lösenordsåterställning](https://passwordreset.microsoftonline.com/).

    ![Can't access your account?][Login] (Kan du inte komma åt ditt konto?)

2. Ange ditt **användar-ID** för arbets- eller skolkontot, bevisa att du inte är en robot genom att ange de tecken som visas på skärmen och klicka sedan på **Nästa**.

   > [!NOTE]
   > Om IT-avdelningen inte har aktiverat den här funktionen visas en länk för att ”kontakta administratören” så att IT-avdelningen kan hjälpa dig via e-post eller en egen portal.
   >
   > Om du behöver låsa upp ditt konto nu välja alternativet ”Jag vet mitt lösenord, men ändå inte logga in”.
   >

3. Beroende på hur IT-personalen har konfigurerat SSPR finns en eller flera av följande autentiseringsmetoder. Du eller din IT-personal ifyllda viss information innan du använder artikeln [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).

   * **Skicka e-post till min alternativa e-postadress**
   * **Skicka ett sms till min mobiltelefon**
   * **Ring min mobiltelefon**
   * **Ring min arbetstelefon**
   * **Svara på mina säkerhetsfrågor**

   Välj ett alternativ, ange rätt svar och klicka på **Nästa**.

   ![Kontrollera dina autentiseringsdata][Verification]

4. Din IT-personal kanske kräver ytterligare verifiering och du kanske måste upprepa steg 3 igen med ett annat alternativ.
5. På sidan **Välj ett nytt lösenord** anger du ett nytt lösenord, bekräftar ditt lösenord och klickar sedan på **Slutför**. Ditt lösenord för arbetet eller skolan kanske vissa krav. Vi rekommenderar att ditt lösenord vara 8-16 tecken med versaler och gemener, siffror och specialtecken.
6. När du ser **Ditt lösenord har återställts** kan du logga in med ditt nya lösenord.

    ![Ditt lösenord har återställts][Complete]

Nu kan du normalt komma åt ditt konto. Om du inte kan det ber du IT-personalen i din organisation om hjälp.

Du kan få ett e-postbekräftelse som kommer från ett konto som ”Microsoft på uppdrag av \<organisationen >”. Om du får ett liknande e-postmeddelande och inte använde lösenordsåterställning via självbetjäning för att få åtkomst till ditt konto, kontaktar du IT-personalen i din organisation.

## <a name="change-my-password"></a>Ändra mitt lösenord

Om du redan känner till lösenordet och behöver ändra det provar du stegen nedan för att ändra lösenordet.

### <a name="change-your-password-from-the-office-365-portal"></a>Ändra ditt lösenord från Office 365-portalen

Använd den här metoden om du normalt kommer åt dina program med hjälp av Office-portalen

1. Logga in på ditt [Office 365-konto](https://www.office.com) med ditt befintliga lösenord
2. Klicka på din profil längst upp till höger och klicka sedan på **Visa konto**
3. Klicka på **Säkerhet och sekretess** > **Lösenord**
4. Ange ditt gamla lösenord, ange och bekräfta det nya lösenordet och klicka sedan på **Skicka**

### <a name="change-your-password-from-the-azure-access-panel"></a>Ändra ditt lösenord från Azures åtkomstpanel

Använd den här metoden om du normalt kommer åt dina program från Azure Access Portal

1. Logga in på [åtkomstportalen i Azure](https://myapps.microsoft.com/) med ditt befintliga lösenord
2. Klicka på din profil längst upp till höger och klicka sedan på **Profil**
3. Klicka på **Ändra lösenord**
4. Ange ditt gamla lösenord, ange och bekräfta det nya lösenordet och klicka sedan på **Skicka**

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer tillfällen vanliga fel och lösningar:

| Fel-fall| Vilka fel ser du?| Lösning |
| --- | --- | --- |
| En ”Kontakta din administratör” sida kommer när du har angett mitt användar-ID | Kontakta administratören <br> <br> Vi har upptäckt att lösenordet till ditt användarkonto inte hanteras av Microsoft. Vi kan därför inte att återställa lösenordet automatiskt. <br> <br> Du måste kontakta din IT-personal för ytterligare hjälp. | Du ser det här meddelandet eftersom din IT-personal hanterar lösenordet i din lokala miljö och tillåter inte att återställa ditt lösenord från den kan inte komma åt ditt kontolänk. <br> <br> Kontakta din IT-personal direkt för att få hjälp om du vill återställa ditt lösenord och meddela dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig.|
| Jag felmeddelandet ”ditt konto har inte aktiverats för lösenordsåterställning” när du har angett mitt användar-ID | Ditt konto har inte aktiverats för lösenordsåterställning <br> <br> Vi beklagar, men din IT-personal inte har lagt upp ditt konto för användning med den här tjänsten. <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du ser det här meddelandet eftersom din IT-personal inte har aktiverats för återställning av lösenord för din organisation från den kan inte komma åt ditt kontolänk eller har inte licensierats du om du vill använda funktionen. <br> <br> Om du vill återställa ditt lösenord, klickar du på kontakten en administratör länk för att skicka ett e-postmeddelande till ditt företag har IT-personal och meddela dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig. |
| Jag felmeddelandet ”Det gick inte att verifiera ditt konto” när du har angett mitt användar-ID | Det gick inte att verifiera ditt konto <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du ser det här meddelandet eftersom du är aktiverade för återställning av lösenord, men du har inte registrerats för att använda tjänsten. Registrera för återställning av lösenord genom att gå till http://aka.ms/ssprsetup när du har återupprättats åtkomst till ditt konto. <br> <br> Om du vill återställa ditt lösenord, klickar du på kontakten en administratör länk för att skicka ett e-postmeddelande till ditt företags IT-personal. |

## <a name="next-steps"></a>Nästa steg

* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md)
* [Registreringssida för lösenordsåterställning](http://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)
* [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Inloggningssida – Kan du inte komma åt ditt konto?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Kontrollera dina autentiseringsdata"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Ändra ditt lösenord"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Ditt lösenord har återställts"
