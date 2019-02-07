---
title: Hantera lösenord i Azure Active Directory | Microsoft Docs
description: Den här sidan hjälper användarna att förstå vad applösenord är och hur de används med hänsyn till tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 12998547c5525a7132bc350dbbaa7016e4bbeaf0
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767704"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Hantera lösenord för tvåstegsverifiering

Vissa icke-webbläsarappar, till exempel Outlook 2010 inte stöder tvåstegsverifiering. Den här avsaknaden av stöd innebär att appen inte fungerar om du använder tvåstegsverifiering. För att undvika det här problemet, kan du skapa ett automatiskt genererat lösenord ska användas med varje icke-Webbläsarprogrammet, separat från normala lösenordet.

När du använder lösenord, är det viktigt att komma ihåg:

- Applösenord är automatiskt genererade och endast anges en gång per app.

- Det finns en gräns på 40 lösenord per användare. Om du försöker skapa en efter att den gränsen, uppmanas du att ta bort ett befintligt lösenord innan de kan skapa en ny.

- Använda ett applösenord per enhet, inte per app. Till exempel skapa ett enda lösenord för alla appar på din bärbara dator och sedan ett annat lösenord till alla appar på ditt skrivbord.

    >[!Note]
    >Office 2013-klienter (inklusive Outlook) har stöd för nya autentiseringsprotokoll och kan användas med tvåstegsverifiering. Det här stödet innebär att när tvåstegsverifiering är aktiverad, du behöver inte längre applösenord för Office 2013-klienter. Mer information finns i den [hur modern autentisering fungerar för Office 2013 och Office 2016 klientappar](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artikeln.

## <a name="where-to-create-and-delete-your-app-passwords"></a>Var du vill skapa och ta bort dina applösenord

Du får ett applösenord under inledande tvåstegsverifiering verifiering registreringen. Om du behöver fler än en lösenordet kan skapa du ytterligare lösenord, baserat på hur du använder tvåstegsverifiering:

- **Du kan använda tvåstegsverifiering med ditt arbets- eller skolkonto och MyApps-portalen.** Skapa och ta bort ditt lösenord med hjälp av anvisningarna i den [skapa och ta bort applösenord med hjälp av MyApps-portalen](#create-and-delete-app-passwords-using-the-myapps-portal) i den här artikeln. Mer information om MyApps-portalen och hur du använder den finns i [vad är MyApps-portalen i Azure Active Directory?](active-directory-saas-access-panel-introduction.md).

- **Du kan använda tvåstegsverifiering med ditt arbets- eller skolkonto och Office 365-portalen.** Skapa och ta bort ditt lösenord med hjälp av anvisningarna i den [skapa och ta bort applösenord med hjälp av Office 365-portalen](#create-and-delete-app-passwords-using-the-office-365-portal) i den här artikeln.

- **Du kan använda tvåstegsverifiering med ditt personliga Microsoft-konto.** Skapa och ta bort ditt lösenord med hjälp av den [Säkerhetsgrunder](https://account.microsoft.com/account/) sida med ditt personliga Microsoft-konto. Mer information finns i den [applösenord och tvåstegsverifiering](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) artikeln.

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Skapa och ta bort lösenord med hjälp av MyApps-portalen
Du kan skapa och ta bort lösenord via MyApps-portalen.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Skapa ett applösenord med hjälp av MyApps-portalen

1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com).

2. Välj namnet på din längst upp till höger och välj **profil**.

3. Välj **ytterligare säkerhetsverifiering**.

   ![Välj ytterligare säkerhetskontroll – skärmbild](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Välj **applösenord**.

   ![Välj applösenord – skärmbild](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Klicka på **Skapa**.

6. Skriv ett namn för applösenordet och välj sedan **nästa**.

7. Kopiera applösenordet till Urklipp och klistra in den i din app.
   
    ![Skapa ett applösenord](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Att ta bort ett applösenord med hjälp av MyApps-portalen

1. Gå till din profil och välj sedan **ytterligare säkerhetskontroll**.

2. Välj **applösenord**, och välj sedan **ta bort** bredvid applösenordet som du vill ta bort.

   ![Ta bort ett applösenord](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Välj **Ja** att bekräfta att du vill ta bort lösenordet och välj sedan **Stäng**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Skapa och ta bort lösenord med hjälp av Office 365-portalen

Om du använder tvåstegsverifiering med ditt arbets- eller skolkonto och Office 365-appar, kan du skapa och ta bort ditt lösenord med hjälp av Office 365-portalen. Du kan ha högst 40 lösenord åt gången. Om du behöver ett nytt applösenord efter den gränsen, måste du ta bort en av dina befintliga applösenord.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Att skapa applösenord med hjälp av Office 365-portalen

1. Logga in på ditt arbets- eller skolkonto konto.

2. Gå till https://portal.office.comväljer den **inställningar** ikonen till höger på den **Office 365-portalen** sidan och expandera sedan **ytterligare säkerhetsverifiering**.

    ![Office-portalen med utökat ytterligare verifiering säkerhetsområde](media/security-info/security-info-o365password.png)

3. Välj där det står, **skapa och hantera applösenord** att öppna den **applösenord** sidan.

4. Välj **skapa**, ange ett eget namn för den app som behöver applösenordet och välj sedan **nästa**.

5. Välj **kopiera lösenord till Urklipp**, och välj sedan **Stäng**.

6. Använd kopierade applösenordet för att logga in på icke-webbläsarbaserade appen. Du behöver bara ange detta lösenord en gång och det sparas i framtiden.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Ta bort lösenord med hjälp av Office 365-portalen

1. Logga in på ditt arbets- eller skolkonto konto.

2. Gå till https://portal.office.comväljer den **inställningar** ikonen till höger på den **Office 365-portalen** och välj sedan **ytterligare säkerhetsverifiering**.

3. Välj där det står, **skapa och hantera applösenord** att öppna den **applösenord** sidan.

4. Välj **ta bort** att ta bort applösenordet, väljer **Ja** i dialogrutan och välj sedan **Stäng**.

    Applösenordet har tagits bort.

5. Följ stegen för att skapa ett applösenord för att skapa ditt nya applösenord.

## <a name="if-your-app-passwords-arent-working-properly"></a>Om dina applösenord inte fungerar korrekt

Kontrollera att du skrivit rätt lösenord. Om du är säker på att du har angett lösenordet korrekt kan försöka du att logga in igen och skapa ett nytt applösenord. Om inget av dessa alternativ kan åtgärda problemet kan du kontakta företagets support så att de kan ta bort dina befintliga applösenord, så att du skapar en helt ny. 

## <a name="next-steps"></a>Nästa steg

- [Hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md)

- Prova att använda den [Microsoft Authenticator-appen](user-help-auth-app-download-install.md) att verifiera dina inloggningar med appmeddelanden, i stället för att ta emot skickar ett SMS eller samtal.
