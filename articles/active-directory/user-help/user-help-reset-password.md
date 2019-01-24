---
title: Återställa lösenordet med hjälp av säkerhetsinformation - Azure Active Directory | Microsoft Docs
description: Så här att återställa ditt eget lösenord om du glömmer det, med hjälp av din säkerhetskontroll för information och tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 6bf8c548d81b3cade487f3188cdafae82b5776d9
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815947"
---
# <a name="reset-your-work-or-school-password"></a>Återställa lösenordet för arbets- eller skolkonto

Om du har glömt ditt lösenord, inte har fått något från företagets support eller har blivit utelåst från ditt konto kan du använda din säkerhetsinformation och din mobila enhet för att återställa lösenordet.

>[!NOTE]
>Om du vet ditt lösenord och bara vill ändra den, går du till den [ändra ditt lösenord](#how-to-change-your-password) stegen i den här artikeln.<br><br>
>Om du försöker komma åt ett personligt konto som Xbox, hotmail.com eller outlook.com provar i den [när du inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Hur du återställa eller låsa upp lösenordet för ett arbets- eller skolkonto konto

Om du inte komma åt ditt konto i Azure Active Directory (AD Azure), det kan bero på att antingen:

- Ditt lösenord fungerar inte och du vill återställa den, eller

- Du vet ditt lösenord, men ditt konto är låst och du behöver att låsa upp den.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Att återställa ditt lösenord och komma åt ditt konto

1. I den **ange lösenord** väljer **har glömt mitt lösenord**.

2. I den **komma åt ditt konto** skärmen, Skriv ditt arbete eller din skola **användar-ID** (t.ex, din e-postadress), bevisa att du inte är en robot genom att ange de tecken du visas på skärmen och välj sedan  **Nästa**.

   ![Växla tillbaka till skärmen konto](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Om administratören inte har aktiverat möjligheten att återställa ditt eget lösenord, visas en **Kontakta administratören** länkar i stället för den **komma åt ditt konto** skärmen. Den här länken kan du kontakta din administratör om hur du återställer ditt lösenord, via e-post eller en webbportal.

3. Välj något av följande metoder för att verifiera din identitet och ändra ditt lösenord. Beroende på hur administratören har konfigurerat din organisation kan behöva du gå igenom den här processen en gång till, att lägga till information för ett andra verifieringssteg.

    ![Komma åt ditt konto, verifiering av steg #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Beroende på hur administratören har konfigurerat din organisation, kanske vissa av dessa verifieringsalternativ inte tillgängliga. Du har tidigare ställa in din mobila enhet för verifiering med minst en av dessa metoder.<br><br>Ditt nya lösenord kan dessutom behöva uppfylla vissa krav. Starka lösenord har normalt 8 och 16 tecken, inklusive versaler och gemener tecken, minst en siffra och minst ett specialtecken.

- **Återställa lösenordet med hjälp av en e-postadress.** Skickar ett e-postmeddelande till den e-postadress som du tidigare har konfigurerat i tvåstegsverifiering eller säkerhetsinformation. Om din administratör har aktiverat info säkerhetsupplevelse, hittar du mer information om hur du konfigurerar en e-postadress i den [konfigurera säkerhetsinformation för att använda e-post (förhandsversion)](security-info-setup-email.md) artikeln. Om du använder har ännu inte säkerhetsinformation, hittar du mer information om hur du konfigurerar en e-postadress i den [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) artikeln. 

    1. Välj **e min alternativa e-postadress**, och välj sedan **e-post**.

    2. Ange verifieringskoden från e-postmeddelandet i rutan och välj sedan **nästa**.
    
    3. Skriv och bekräfta det nya lösenordet och välj sedan **Slutför**.

- **Återställa lösenordet med hjälp av ett textmeddelande.** Skickar ett SMS till det telefonnummer som du tidigare har konfigurerat säkerhetsinformation. Om din administratör har aktiverat info säkerhetsupplevelse, hittar du mer information om hur du konfigurerar textmeddelanden i den [konfigurera säkerhetsinformation för att använda textmeddelanden (förhandsversion)](security-info-setup-text-msg.md) artikeln. Om du använder har ännu inte säkerhetsinformation, hittar du mer information om hur du konfigurerar textmeddelanden i den [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) artikeln.

    1. Välj **texten min mobiltelefon**, ange ditt telefonnummer och välj sedan **Text**.

    2. Ange verifieringskoden från på SMS: et i rutan och välj sedan **nästa**.

    3. Skriv och bekräfta det nya lösenordet och välj sedan **Slutför**.

- **Återställa lösenordet med hjälp av ett telefonnummer.** Skickar ett SMS till det telefonnummer som du tidigare har konfigurerat säkerhetsinformation. Om din administratör har aktiverat info säkerhetsupplevelse, hittar du mer information om hur du konfigurerar ett telefonnummer i den [konfigurera säkerhetsinformation för att använda ett telefonsamtal (förhandsversion)](security-info-setup-phone-number.md) artikeln. Om du använder har ännu inte säkerhetsinformation, hittar du mer information om hur du konfigurerar ett telefonnummer i den [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) artikeln.

    1. Välj **Ring min mobiltelefon**, ange ditt telefonnummer och välj sedan **anropa**.

    2. Besvara samtalet och följ anvisningarna för att verifiera din identitet och välj sedan **nästa**.

    3. Skriv och bekräfta det nya lösenordet och välj sedan **Slutför**.

- **Återställa lösenordet med säkerhetsfrågor.** Visar listan över säkerhetsfrågor som du ställer in säkerhetsinformation. Om din administratör har aktiverat info säkerhetsupplevelse, hittar du mer information om hur du konfigurerar dina säkerhetsfrågor i den [konfigurera säkerhetsinformation kan använda fördefinierade säkerhetsfrågor (förhandsversion)](security-info-setup-questions.md) artikeln. Om du använder har ännu inte säkerhetsinformation, hittar du mer information om hur du konfigurerar säkerhetsfrågor i den [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) artikeln.

    1. Välj **svara på Mina säkerhetsfrågor**, besvara frågor och välj sedan **nästa**.

    2. Skriv och bekräfta det nya lösenordet och välj sedan **Slutför**.

- **Återställa lösenordet med hjälp av ett meddelande från din autentiseringsapp.** Skickar ett meddelande om godkännande till authenticator-appen. Om din administratör har aktiverat info säkerhetsupplevelse, hittar du mer information om hur du konfigurerar en autentiseringsapp kan skicka ett meddelande den [ställa in säkerhetsinformation för att använda en authentication-appen (förhandsversion)](security-info-setup-auth-app.md) artikeln. Om du använder har ännu inte säkerhetsinformation, hittar du mer information om hur du konfigurerar en autentiseringsapp kan skicka ett meddelande den [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) artikeln.

    1. Välj **Godkänn ett meddelande på min autentiserare**, och välj sedan **skicka meddelande**.

    2. Godkänn inloggning från authenticator-appen.

    3. Skriv och bekräfta det nya lösenordet och välj sedan **Slutför**.

- **Återställa lösenordet med hjälp av en kod från din autentiseringsapp.** Accepterar en slumpmässig kod som tillhandahålls av din app med autentisering. Om din administratör har aktiverat info säkerhetsupplevelse, hittar du mer information om hur du konfigurerar en autentiseringsapp ange en kod i den [ställa in säkerhetsinformation för att använda en authentication-appen (förhandsversion)](security-info-setup-auth-app.md) artikeln. Om du använder har ännu inte säkerhetsinformation, hittar du mer information om hur du konfigurerar en autentiseringsapp ange en kod i den [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) artikeln.

    1. Välj **ange en kod från min autentiserare**, och välj sedan **skicka meddelande**.

    2. Öppna autentiseringsappen, Skriv Verifieringskod för ditt konto i rutan och välj sedan **nästa**.

    3. Skriv och bekräfta det nya lösenordet och välj sedan **Slutför**.

    4. När du får ett meddelande som säger att ditt lösenord har återställts kan du logga in till ditt konto med ditt nya lösenord.
        
    Om du fortfarande inte komma åt ditt konto kontaktar du din organisations administratör för mer hjälp.

När du återställer ditt lösenord, kan du få en bekräftelse via e-post från ett konto som ”, Microsoft på uppdrag av \< *your_organization*>”. Om du får ett liknande e-postmeddelande, men du inte återställa ditt lösenord nyligen, måste du kontakta organisationens administratör omedelbart.

## <a name="how-to-change-your-password"></a>Hur du ändrar ditt lösenord

Om du vill ändra ditt lösenord kan du göra det via Office 365-portalen, Azures åtkomstpanel eller Windows 10 på inloggningssidan.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Ändra ditt lösenord med hjälp av Office 365-portalen

Använd den här metoden om du normalt kommer åt dina appar via Office-portalen:

1. Logga in på din [Office 365-konto](https://www.office.com), med ditt befintliga lösenord.

2. Välj din profil längst upp till höger och välj sedan **visa konto**.

3. Välj **säkerhet och sekretess** > **lösenord**.

4. Ange ditt gamla lösenord, skapa och bekräfta det nya lösenordet och välj sedan **skicka**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Ändra ditt lösenord från Azures åtkomstpanel

Använd den här metoden om du normalt inte komma åt dina appar från Azures åtkomstpanel (MyApps):

1. Logga in på den [Azures åtkomstpanel](https://myapps.microsoft.com/), med ditt befintliga lösenord.

2. Välj din profil längst upp till höger och välj sedan **profil**.

3. Välj **ändra lösenord**.

4. Ange ditt gamla lösenord, skapa och bekräfta det nya lösenordet och välj sedan **skicka**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Ändra ditt lösenord på Windows-inloggning

Om administratören aktiverat funktionen, kan du se en länk till **Återställ lösenord** på din Windows 7, Windows 8, Windows 8.1 eller Windows 10-inloggningsskärmen.

1. Välj den **Återställ lösenord** länk för att starta lösenordet återställa lösenordet utan att använda den normala webbaserad upplevelsen.

2. Bekräfta ditt användar-ID och välj **nästa**.

3. Välj och bekräfta en kontaktmetod för verifiering. Om det behövs kan du välja ett andra verifieringsalternativ som skiljer sig från din tidigare påverkas, fylla i informationen som behövs.

4. På den **skapa ett nytt lösenord** sidan, Skriv och bekräfta det nya lösenordet och välj sedan **nästa**.

    Starka lösenord har normalt 8 och 16 tecken, inklusive versaler och gemener tecken, minst en siffra och minst ett specialtecken.

5. När du får ett meddelande som säger att ditt lösenord har återställts kan du välja **Slutför**.

    Om du fortfarande inte komma åt ditt konto kontaktar du din organisations administratör för mer hjälp.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

Här följer några vanliga fel och sina lösningar:

|Problem|Beskrivning|Lösning|
| --- | --- | --- |
|Det uppstår ett fel när jag försöker ändra mitt lösenord. |Ditt lösenord innehåller ett ord, en fras eller ett mönster som gör det enkelt att gissa ditt lösenord.| Försök igen med ett starkare lösenord.|
|När jag anger mitt användar-ID, gå jag till en sida med texten ”Kontakta din administratör”.|Microsoft har fastställt att lösenordet till ditt användarkonto hanteras av administratören i en lokal miljö. Därför kan återställa du inte lösenordet från länken ”det går inte att komma åt ditt konto”. |Kontakta administratören om du behöver mer hjälp.|
|När jag anger mitt användar-ID, uppstår ett fel med texten ”ditt konto har inte aktiverats för återställning av lösenord”.|Din administratör har inte konfigurerat ditt konto så att du kan återställa ditt eget lösenord.|Administratören har inte aktiverat återställning av lösenord för din organisation från länken ”det går inte att komma åt ditt konto” eller har inte licensierats du om du vill använda funktionen.<br><br> Om du vill återställa ditt lösenord, måste du välja ”kontakta en administratör länken” att skicka ett e-postmeddelande till ditt företags administratör och informera dem om du vill återställa ditt lösenord.|
|När jag anger mitt användar-ID, uppstår ett fel med texten ”vi kunde inte verifiera ditt konto”.|Det gick inte att kontrollera din kontoinformation logga in igen.|Det finns två skäl som du kan se det här meddelandet.<br><br>1. Administratören aktiverat återställning av lösenord för din organisation, men du har inte registrerats för användning av tjänsten. Om du vill registrera för återställning av lösenord finns i följande artiklar, baserat på din verifieringsmetod: [Konfigurera säkerhetsinformation för att använda en autentiseringsapp (förhandsversion)](security-info-setup-auth-app.md), [konfigurera säkerhetsinformation för att använda ett telefonsamtal (förhandsversion)](security-info-setup-phone-number.md), [konfigurera säkerhetsinformation för att använda textmeddelanden (förhandsversion)](security-info-setup-text-msg.md), [ Konfigurera säkerhetsinformation för att använda e-post (förhandsversion)](security-info-setup-email.md), eller [konfigurera säkerhetsinformation kan använda säkerhetsfrågor (förhandsversion)](security-info-setup-questions.md).<br><br>2. Administratören har inte aktiverat återställning av lösenord för din organisation. I så fall måste du välja ”kontakta en administratör länken” att skicka ett e-postmeddelande till din administratör om att återställa ditt lösenord.|

## <a name="next-steps"></a>Nästa steg

- Redigera din säkerhetsinformation för att lägga till eller ändra din verifieringsmetoder i den [hantera din säkerhetsinformation (förhandsversion)](security-info-manage-settings.md) artikeln. 

- Lär dig mer om säkerhetsinformation i den [info (förhandsversion) Säkerhetsöversikt](user-help-security-info-overview.md) artikeln.

- Lär dig mer om tvåstegsverifiering i den [tvåstegsverifiering verifiering översikt](user-help-two-step-verification-overview.md) artikeln. 

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.