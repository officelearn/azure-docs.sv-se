---
title: Återställ ditt lösenord med hjälp av säkerhetsinformation - Azure Active Directory | Microsoft-dokument
description: Så här återställer du ditt eget lösenord om du glömmer det, med hjälp av din säkerhetsinformation och tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 3ed79be318319009aabb1b1ef0c42c4021bbbabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062667"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Återställa ditt arbets- eller skollösenord med hjälp av säkerhetsinformation

Om du har glömt ditt arbets- eller skollösenord, aldrig fått något lösenord från din organisation eller har låsts ute från ditt konto kan du använda din säkerhetsinformation och din mobila enhet för att återställa ditt arbets- eller skollösenord. Administratören måste aktivera den här funktionen för att du ska kunna konfigurera informationen och återställa ditt eget lösenord.

Om du känner till ditt lösenord, men vill ändra det, läser du avsnitten [Ändra lösenordssteg](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password) i den här artikeln.

>[!Important]
>Den här artikeln är avsedd för användare som försöker använda återställa ett glömt eller okänt lösenord för arbets- eller skolkonto. Om du är administratör och letar efter information om hur du aktiverar återställning av lösenord för självbetjäning för anställda eller andra användare läser [du återställningen av självbetjäningslösenordet i Azure AD och andra artiklar](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Så här återställer eller låser du upp ditt lösenord för ett arbets- eller skolkonto

Om du inte kan komma åt ditt Azure Active Directory-konto (Azure AD) kan det bero på att antingen:

- Ditt lösenord fungerar inte och du vill återställa det, eller

- Du vet ditt lösenord, men ditt konto är utelåst och du måste låsa upp det.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Så här återställer du lösenordet och går in på ditt konto igen

1. På skärmen **Ange lösenord** väljer du Glömt **mitt lösenord**.

2. På skärmen **Gå in på kontot igen** skriver du ditt **användar-ID** för arbete eller skola (till exempel din e-postadress), bevisar att du inte är en robot genom att ange de tecken du ser på skärmen och välj sedan **Nästa**.

   ![Gå tillbaka till din kontoskärm](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Om administratören inte har aktiverat möjligheten för dig att återställa ditt eget lösenord visas länken **Kontakta administratören** i stället för skärmen **Hämta tillbaka till ditt konto.** Med den här länken kan du kontakta administratören om hur du återställer lösenordet, antingen via e-post eller en webbportal.

3. Välj en av följande metoder för att verifiera din identitet och ändra ditt lösenord. Beroende på hur administratören har konfigurerat din organisation kan du behöva gå igenom den här processen en andra gång och lägga till information för ett andra verifieringssteg.

    ![Kom tillbaka till ditt konto, verifieringssteg #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Beroende på hur administratören har konfigurerat din organisation kanske vissa av dessa verifieringsalternativ inte är tillgängliga. Du måste tidigare ha konfigurerat din mobila enhet för verifiering med minst en av dessa metoder.<br><br>Dessutom kan ditt nya lösenord behöva uppfylla vissa styrkaskrav. Starka lösenord har vanligtvis 8 till 16 tecken, inklusive versaler och gemener, minst ett nummer och minst ett specialtecken.

- **Återställ ditt lösenord med hjälp av en e-postadress.** Skickar ett e-postmeddelande till den e-postadress som du tidigare har angett i tvåstegsverifiering eller säkerhetsinformation. Om administratören har aktiverat säkerhetsinformationsupplevelsen kan du hitta mer information om hur du konfigurerar en e-postadress i artikeln [Konfigurera säkerhetsinformation för att använda e-post (förhandsversion).](security-info-setup-email.md) Om du ännu inte använder säkerhetsinformation kan du hitta mer information om hur du konfigurerar en e-postadress i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md) 

    1. Välj **E-post min alternativa e-post**och välj sedan **E-post**.

    2. Skriv verifieringskoden från e-postmeddelandet i rutan och välj sedan **Nästa**.

    3. Skriv och bekräfta ditt nya lösenord och välj sedan **Slutför**.

- **Återställ lösenordet med hjälp av ett textmeddelande.** Skickar ett sms till det telefonnummer som du tidigare har angett i säkerhetsinformation. Om administratören har aktiverat säkerhetsinformationsupplevelsen kan du hitta mer information om hur du konfigurerar textmeddelanden i artikeln [Konfigurera säkerhetsinformation för att använda sms(förhandsversion).](security-info-setup-text-msg.md) Om du ännu inte använder säkerhetsinformation kan du hitta mer information om hur du konfigurerar textmeddelanden i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)

    1. Välj **Text min mobiltelefon,** skriv ditt telefonnummer och välj sedan **Text**.

    2. Skriv verifieringskoden från sms:et i rutan och välj sedan **Nästa**.

    3. Skriv och bekräfta ditt nya lösenord och välj sedan **Slutför**.

- **Återställ lösenordet med ett telefonnummer.** Skickar ett sms till det telefonnummer som du tidigare har angett i säkerhetsinformation. Om administratören har aktiverat säkerhetsinformationen kan du hitta mer information om hur du konfigurerar ett telefonnummer i artikeln [Konfigurera säkerhetsinformation för att använda ett telefonsamtal (förhandsversion).](security-info-setup-phone-number.md) Om du ännu inte använder säkerhetsinformation kan du hitta mer information om hur du konfigurerar ett telefonnummer i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)

    1. Välj **Ring min mobiltelefon,** skriv ditt telefonnummer och välj sedan **Ring**.

    2. Besvara telefonsamtalet och följ instruktionerna för att verifiera din identitet och välj sedan **Nästa**.

    3. Skriv och bekräfta ditt nya lösenord och välj sedan **Slutför**.

- **Återställ ditt lösenord med hjälp av säkerhetsfrågor.** Visar listan över säkerhetsfrågor som du har ställt in i säkerhetsinformation. Om administratören har aktiverat säkerhetsinformationsupplevelsen kan du hitta mer information om hur du konfigurerar dina säkerhetsfrågor i artikeln [Konfigurera säkerhetsinformation för att använda fördefinierade säkerhetsfrågor (förhandsversion).](security-info-setup-questions.md) Om du ännu inte använder säkerhetsinformation kan du hitta mer information om hur du ställer in säkerhetsfrågor i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)

    1. Välj **Svara på mina säkerhetsfrågor,** svara på frågorna och välj sedan **Nästa**.

    2. Skriv och bekräfta ditt nya lösenord och välj sedan **Slutför**.

- **Återställ ditt lösenord med hjälp av ett meddelande från din autentiseringsapp.** Skickar ett godkännandemeddelande till autentiseringsappen. Om administratören har aktiverat säkerhetsinformationsupplevelsen kan du hitta mer information om hur du konfigurerar en autentiseringsapp för att skicka ett meddelande i artikeln [Konfigurera säkerhetsinformation för att använda en förhandsgranskningsartikel (förhandsversion).](security-info-setup-auth-app.md) Om du ännu inte använder säkerhetsinformation kan du hitta mer information om hur du konfigurerar en autentiseringsapp för att skicka ett meddelande i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)

    1. Välj **Godkänn ett meddelande i min autentiseringsapp**och välj sedan Skicka **meddelande**.

    2. Godkänn inloggningen från din autentiseringsapp.

    3. Skriv och bekräfta ditt nya lösenord och välj sedan **Slutför**.

- **Återställ ditt lösenord med hjälp av en kod från din autentiseringsapp.** Accepterar en slumpmässig kod som tillhandahålls av din autentiseringsapp. Om administratören har aktiverat säkerhetsinformationsupplevelsen kan du hitta mer information om hur du konfigurerar en autentiseringsapp för att tillhandahålla en kod i artikeln [Konfigurera säkerhetsinformation för att använda en förhandsgranskningsartikel för autentiseringsappar.](security-info-setup-auth-app.md) Om du ännu inte använder säkerhetsinformation kan du hitta mer information om hur du konfigurerar en autentiseringsapp för att tillhandahålla en kod i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)

  1. Välj **Ange en kod från min autentiseringsapp**och välj sedan Skicka **meddelande**.

  2. Öppna autentiseringsappen, skriv verifieringskoden för ditt konto i rutan och välj sedan **Nästa**.

  3. Skriv och bekräfta ditt nya lösenord och välj sedan **Slutför**.

  4. När du har fått meddelandet om att lösenordet har återställts kan du logga in på ditt konto med ditt nya lösenord.

     Om du fortfarande inte kan komma åt ditt konto bör du kontakta organisationens administratör för mer hjälp.

När du har återställt lösenordet kan du få ett bekräftelsemeddelande som \<kommer från ett konto som "Microsoft för *your_organization*>". Om du får ett liknande e-postmeddelande, men du inte nyligen har återställt lösenordet, måste du kontakta organisationens administratör omedelbart.

## <a name="how-to-change-your-password"></a>Så ändrar du ditt lösenord

Om du bara vill ändra ditt lösenord kan du göra det via Office 365-portalen, Azure Access Panel eller inloggningssidan för Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Så här ändrar du ditt lösenord med Office 365-portalen

Använd den här metoden om du vanligtvis kommer åt dina appar via Office-portalen:

1. Logga in på ditt [Office 365-konto](https://portal.office.com)med ditt befintliga lösenord.

2. Välj din profil längst upp till höger och välj sedan **Visa konto**.

3. Välj **sekretesslösenord** > **Password**för & .

4. Skriv ditt gamla lösenord, skapa och bekräfta ditt nya lösenord och välj sedan **Skicka**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Så här ändrar du ditt lösenord från Azure Access Panel

Använd den här metoden om du vanligtvis kommer åt dina appar från Azure Access Panel (MyApps):

1. Logga in på [Azure Access Panel](https://myapps.microsoft.com/)med ditt befintliga lösenord.

2. Välj din profil längst upp till höger och välj sedan **Profil**.

3. Välj **Ändra lösenord**.

4. Skriv ditt gamla lösenord, skapa och bekräfta ditt nya lösenord och välj sedan **Skicka**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Så här ändrar du ditt lösenord vid Windows-inloggning

Om administratören har aktiverat funktionen kan du se en länk för att **återställa lösenord** på inloggningsskärmen för Windows 7, Windows 8, Windows 8.1 eller Windows 10.

1. Välj länken **Återställ lösenord** för att starta återställningsprocessen för lösenord utan att behöva använda den normala webbaserade upplevelsen.

2. Bekräfta ditt användar-ID och välj **Nästa**.

3. Välj och bekräfta en kontaktmetod för verifiering. Om det behövs väljer du ett andra verifieringsalternativ som skiljer sig från det tidigare, och fyller i nödvändig information.

4. På sidan **Skapa ett nytt lösenord** skriver och bekräftar du ditt nya lösenord och väljer sedan **Nästa**.

    Starka lösenord har vanligtvis 8 till 16 tecken, inklusive versaler och gemener, minst ett nummer och minst ett specialtecken.

5. När du har fått meddelandet om att lösenordet har återställts kan du välja **Slutför**.

    Om du fortfarande inte kan komma åt ditt konto bör du kontakta organisationens administratör för mer hjälp.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

Här är några vanliga felfall och deras lösningar:

|Problem|Beskrivning|Lösning|
| --- | --- | --- |
|När jag försöker ändra mitt lösenord får jag ett felmeddelande. |Ditt lösenord har ett ord, en fras eller ett mönster som gör att ditt lösenord inte kan gissas.| Försök igen med ett starkare lösenord.|
|När jag har angett mitt användar-ID går jag till en sida där det står :"Kontakta administratören.",|Microsoft har fastställt att lösenordet för ditt användarkonto hanteras av administratören i en lokal miljö. Därför kan du inte återställa lösenordet från länken "Kan inte komma åt ditt konto". |Kontakta administratören för mer hjälp.|
|När jag har angett mitt användar-ID får jag ett felmeddelande med det står: "Ditt konto är inte aktiverat för återställning av lösenord."|Administratören har inte konfigurerat ditt konto så att du kan återställa ditt eget lösenord.|Administratören har inte aktiverat återställning av lösenord för din organisation från länken "Kan inte komma åt ditt konto" eller har inte licensierat dig att använda funktionen.<br><br> Om du vill återställa lösenordet måste du välja "kontakta en administratörslänk" för att skicka ett e-postmeddelande till företagets administratör och meddela dem att du vill återställa lösenordet.|
|När jag har angett mitt användar-ID får jag ett felmeddelande med det står: "Vi kunde inte verifiera ditt konto."|Inloggningsprocessen kunde inte verifiera din kontoinformation.|Det finns två anledningar till att du kan se det här meddelandet.<br><br>1. Administratören har aktiverat återställning av lösenord för din organisation, men du har inte registrerat dig för att använda tjänsten. Om du vill registrera dig för återställning av lösenord läser du någon av följande artiklar, baserat på din verifieringsmetod: [Konfigurera säkerhetsinformation för att använda en autentiseringsapp (förhandsgranskning)](security-info-setup-auth-app.md), [Konfigurera säkerhetsinformation för att använda ett telefonsamtal (förhandsgranskning)](security-info-setup-phone-number.md), [Konfigurera säkerhetsinformation för att använda textmeddelanden (förhandsgranskning)](security-info-setup-text-msg.md), [Konfigurera säkerhetsinformation för att använda e-post (förhandsgranskning)](security-info-setup-email.md)eller [Konfigurera säkerhetsinformation för att använda säkerhetsfrågor (förhandsgranskning)](security-info-setup-questions.md).<br><br>2. Administratören har inte aktiverat återställning av lösenord för din organisation. I det här fallet måste du välja "kontakta en administratörslänk" för att skicka ett e-postmeddelande till administratören och be att du återställer lösenordet.|

## <a name="next-steps"></a>Nästa steg

- Läs mer om säkerhetsinformation i översiktsartikeln [säkerhetsinformation (förhandsversion).](user-help-security-info-overview.md)

- Om du försöker komma tillbaka till ett personligt konto som Xbox, hotmail.com eller outlook.com kan du prova förslagen i [artikeln När du inte kan logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
