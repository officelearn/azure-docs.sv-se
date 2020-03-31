---
title: Hantera applösenord – Azure Active Directory | Microsoft-dokument
description: Läs mer om applösenord och vad de används för när det gäller tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253226"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Hantera applösenord för tvåstegsverifiering

>[!Important]
>Administratören kanske inte tillåter att du använder applösenord. Om du inte ser **Applösenord** som ett alternativ är de inte tillgängliga i organisationen.

När du använder applösenord är det viktigt att komma ihåg:

- Applösenord genereras automatiskt och ska skapas och anges en gång per app.

- Det finns en gräns på 40 lösenord per användare. Om du försöker skapa ett efter den gränsen uppmanas du att ta bort ett befintligt lösenord innan du får skapa det nya.

    >[!Note]
    >Office 2013-klienter (inklusive Outlook) stöder nya autentiseringsprotokoll och kan användas med tvåstegsverifiering. Det här stödet innebär att när tvåstegsverifiering har aktiverats behöver du inte längre applösenord för Office 2013-klienter. Mer information finns i artikeln [Hur modern autentisering fungerar för Office 2013 och Office 2016-klientappar.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Skapa nya applösenord

Under den första registreringsprocessen för tvåfaktorsverifiering får du ett lösenord för en enda app. Om du behöver mer än en, måste du skapa dem själv. Du kan skapa applösenord från flera områden, beroende på hur tvåfaktorsverifiering har konfigurerats i organisationen. Mer information om hur du registrerar dig för att använda tvåfaktorsverifiering med ditt arbets- eller skolkonto finns i [Översikt för tvåfaktorsverifiering och ditt arbets- eller skolkonto](multi-factor-authentication-end-user-first-time.md) och dess relaterade artiklar.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Här ska du skapa och ta bort applösenord

Du kan skapa och ta bort applösenord, baserat på hur du använder tvåfaktorsverifiering:

- **Din organisation använder tvåfaktorsverifiering och sidan Ytterligare säkerhetsverifiering.** Om du använder ditt arbets- eller skolkonto (till alain@contoso.comexempel) med tvåfaktorsverifiering i organisationen kan du hantera applösenorden från sidan Ytterligare [säkerhetsverifiering](https://account.activedirectory.windowsazure.com/Proofup.aspx). Detaljerade instruktioner finns i [Skapa och ta bort applösenord med hjälp av sidan Ytterligare säkerhetsverifiering](#create-and-delete-app-passwords-from-the-additional-security-verification-page) i den här artikeln.

- **Din organisation använder tvåfaktorsverifiering och Office 365-portalen.** Om du använder ditt arbets- eller skolkonto (till exempel alain@contoso.comtvåfaktorsverifiering och Office 365-appar i organisationen kan du hantera applösenorden från portalsidan för Office [365](https://www.office.com). Detaljerade instruktioner finns i [Skapa och ta bort applösenord med Office 365-portalen](#create-and-delete-app-passwords-using-the-office-365-portal) i den här artikeln.

- **Du använder tvåfaktorsverifiering med ett personligt Microsoft-konto.** Om du använder ett personligt Microsoft-konto alain@outlook.com(till exempel) med tvåfaktorsverifiering kan du hantera applösenorden från [sidan Grundläggande säkerhet](https://account.microsoft.com/security/). Detaljerade instruktioner finns i [Använda applösenord med appar som inte stöder tvåstegsverifiering](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Skapa och ta bort applösenord från sidan Ytterligare säkerhetsverifiering

Du kan skapa och ta bort applösenord från sidan **Ytterligare säkerhetsverifiering** för ditt arbets- eller skolkonto.

1. Logga in på [sidan Ytterligare säkerhetsverifiering](https://account.activedirectory.windowsazure.com/Proofup.aspx)och välj sedan **Applösenord**.

    ![Sidan Applösenord, med fliken Applösenord markerad](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Välj **Skapa**, skriv namnet på appen som kräver applösenordet och välj sedan **Nästa**.

    ![Sidan Skapa applösenord med namnet på appen som behöver lösenord](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Kopiera lösenordet från sidan **Applösenord** och välj sedan **Stäng**.

    ![Sidan Applösenord med lösenordet för den angivna appen](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Kontrollera att appen visas på sidan **Applösenord.**

     ![Sidan Applösenord, med ny app som visas i listan](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Öppna appen som du skapade applösenordet för (till exempel Outlook 2010) och klistra sedan in applösenordet när du blir tillfrågad om det. Du bör bara behöva göra detta en gång per app.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Så här tar du bort ett applösenord med sidan Applösenord

1. På sidan **Applösenord** väljer du **Ta bort bredvid** det applösenord som du vill ta bort.

   ![Ta bort ett applösenord](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Välj **Ja** för att bekräfta att du vill ta bort lösenordet och välj sedan **Stäng**.

    Applösenordet har tagits bort.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Skapa och ta bort applösenord med Office 365-portalen

Om du använder tvåstegsverifiering med ditt arbets- eller skolkonto och dina Office 365-appar kan du skapa och ta bort applösenorden med Office 365-portalen.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Så här skapar du applösenord med Office 365-portalen

1. Logga in på Office 365 och gå sedan till [sidan Mitt konto,](https://portal.office.com)välj **Sekretess för säkerhet &** och expandera sedan Ytterligare **säkerhetsverifiering**.

    ![Office-portal som visar utökat ytterligare säkerhetsverifieringsområde](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Markera texten som säger, **Skapa och hantera applösenord** för att öppna sidan **Applösenord.**

    ![Sidan Applösenord, med fliken Applösenord markerad](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Välj **Skapa**, skriv namnet på appen som kräver applösenordet och välj sedan **Nästa**.

    ![Sidan Skapa applösenord med namnet på appen som behöver lösenord](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Kopiera lösenordet från sidan **Applösenord** och välj sedan **Stäng**.

    ![Sidan Applösenord med lösenordet för den angivna appen](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Kontrollera att appen visas på sidan **Applösenord.**

     ![Sidan Applösenord, med ny app som visas i listan](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Öppna appen som du skapade applösenordet för (till exempel Outlook 2010) och klistra sedan in applösenordet när du blir tillfrågad om det. Du bör bara behöva göra detta en gång per app.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Så här tar du bort applösenord med sidan Applösenord

1. På sidan **Applösenord** väljer du **Ta bort bredvid** det applösenord som du vill ta bort.

   ![Ta bort ett applösenord](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Välj **Ja** i bekräftelserutan och välj sedan **Stäng**.

    Applösenordet har tagits bort.

## <a name="if-your-app-passwords-arent-working-properly"></a>Om dina applösenord inte fungerar som de ska

Kontrollera att du har skrivit in lösenordet korrekt. Om du är säker på att du har angett lösenordet korrekt kan du försöka logga in igen och skapa ett nytt applösenord. Om inget av dessa alternativ löser problemet kontaktar du organisationens supportavdelning så att de kan ta bort dina befintliga applösenord så att du kan skapa helt nya.

## <a name="next-steps"></a>Nästa steg

- [Hantera verifieringsinställningarna i två steg](multi-factor-authentication-end-user-manage-settings.md)

- Testa [Appen Microsoft Authenticator](user-help-auth-app-download-install.md) för att verifiera dina inloggningar med appaviseringar i stället för att ta emot sms eller samtal.
