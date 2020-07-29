---
title: Så här hanterar du applösenord – Azure Active Directory | Microsoft Docs
description: Lär dig mer om applösenord och vad de används för med avseende på tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 8c6a9304927f5d4bcad895b725955c522b60207a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266244"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Hantera applösenord för tvåstegsverifiering

>[!Important]
>Din administratör kanske inte tillåter att du använder applösenord. Om du inte ser **applösenord** som ett alternativ är de inte tillgängliga i din organisation.

När du använder applösenord är det viktigt att komma ihåg:

- Applösenord genereras automatiskt och bör skapas och anges en gång per app.

- Det finns en gräns på 40 lösen ord per användare. Om du försöker skapa en efter den gränsen uppmanas du att ta bort ett befintligt lösen ord innan du får skapa det nya.

    >[!Note]
    >Office 2013-klienter (inklusive Outlook) har stöd för nya autentiseringsprotokoll och kan användas med tvåstegsverifiering. Det här stödet innebär att när tvåstegsverifiering har Aktiver ATS behöver du inte längre applösenord för Office 2013-klienter. Mer information finns i artikeln [så här fungerar modern autentisering för office 2013 och office 2016-klient program](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Skapa nya applösenord

Under den initiala verifierings processen med två faktorer kan du ange ett lösen ord för en app. Om du behöver fler än en måste du skapa dem själv. Du kan skapa applösenord från flera områden, beroende på hur tvåstegsverifiering har kon figurer ATS i din organisation. Mer information om hur du registrerar dig för att använda tvåfaktorautentisering med ditt arbets-eller skol konto finns i [Översikt över tvåstegsverifiering och ditt arbets-eller skol konto](multi-factor-authentication-end-user-first-time.md) och dess relaterade artiklar.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Var du ska skapa och ta bort dina applösenord

Du kan skapa och ta bort applösenord, baserat på hur du använder tvåstegsverifiering:

- **Din organisation använder tvåstegsverifiering och sidan ytterligare säkerhets verifiering.** Om du använder ditt arbets-eller skol konto (till exempel, alain@contoso.com ) med tvåstegsverifiering i din organisation, kan du hantera dina applösenord från [sidan ytterligare säkerhets verifiering](https://account.activedirectory.windowsazure.com/Proofup.aspx). Detaljerade anvisningar finns i [skapa och ta bort applösenord med hjälp av sidan ytterligare säkerhets verifiering](#create-and-delete-app-passwords-from-the-additional-security-verification-page) i den här artikeln.

- **Din organisation använder tvåstegsverifiering och Office 365-portalen.** Om du använder ditt arbets-eller skol konto (t. ex., alain@contoso.com ), tvåstegsverifiering och Office 365-appar i din organisation, kan du hantera dina applösenord från [sidan Office 365-portalen](https://www.office.com). Detaljerade anvisningar finns i [skapa och ta bort applösenord med hjälp av Office 365-portalen](#create-and-delete-app-passwords-using-the-office-365-portal) i den här artikeln.

- **Du använder tvåstegsverifiering med en personlig Microsoft-konto.** Om du använder en personlig Microsoft-konto (till exempel, alain@outlook.com ) med tvåstegsverifiering kan du hantera dina applösenord från [sidan grundläggande säkerhet](https://account.microsoft.com/security/). Detaljerade anvisningar finns i [använda applösenord med appar som inte stöder tvåstegsverifiering](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Skapa och ta bort applösenord från sidan ytterligare säkerhets verifiering

Du kan skapa och ta bort applösenord från sidan **ytterligare säkerhets verifiering** för ditt arbets-eller skol konto.

1. Logga in på [sidan ytterligare säkerhets verifiering](https://account.activedirectory.windowsazure.com/Proofup.aspx)och välj sedan **applösenord**.

    ![Sidan applösenord med fliken applösenord markerad](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Välj **skapa**, ange namnet på appen som kräver appens lösen ord och välj sedan **Nästa**.

    ![Sidan skapa applösenord, med namnet på appen som behöver lösen ordet](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Kopiera lösen ordet från sidan **ditt applösenord** och välj sedan **Stäng**.

    ![Sidan med ditt applösenord med lösen ordet för din angivna app](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. På sidan **applösenord** kontrollerar du att din app visas.

     ![Sidan applösenord, med en ny app som visas i listan](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Öppna appen som du skapade applösenord för (till exempel Outlook 2010) och klistra in appens lösen ord när du uppmanas till det. Du behöver bara göra detta en gång per app.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Ta bort ett applösenord med hjälp av sidan applösenord

1. På sidan **applösenord** väljer du **ta bort** bredvid det applösenord som du vill ta bort.

   ![Ta bort ett applösenord](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Välj **Ja** för att bekräfta att du vill ta bort lösen ordet och välj sedan **Stäng**.

    Lösen ordet för appen har tagits bort.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Skapa och ta bort applösenord med Office 365-portalen

Om du använder tvåstegsverifiering med ditt arbets-eller skol konto och dina Office 365-appar kan du skapa och ta bort dina applösenord med hjälp av Office 365-portalen.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Så här skapar du applösenord med Office 365-portalen

1. Logga in på Office 365 och gå sedan till [sidan mitt konto](https://portal.office.com), välj **säkerhet & sekretess**och expandera sedan **ytterligare säkerhets verifiering**.

    ![Office-portalen visar utökade ytterligare säkerhets verifierings områden](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Välj den text som säger, **skapa och hantera applösenord** för att öppna sidan **applösenord** .

    ![Sidan applösenord med fliken applösenord markerad](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Välj **skapa**, ange namnet på appen som kräver appens lösen ord och välj sedan **Nästa**.

    ![Sidan skapa applösenord, med namnet på appen som behöver lösen ordet](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Kopiera lösen ordet från sidan **ditt applösenord** och välj sedan **Stäng**.

    ![Sidan med ditt applösenord med lösen ordet för din angivna app](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. På sidan **applösenord** kontrollerar du att din app visas.

     ![Sidan applösenord, med en ny app som visas i listan](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Öppna appen som du skapade applösenord för (till exempel Outlook 2010) och klistra in appens lösen ord när du uppmanas till det. Du behöver bara göra detta en gång per app.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Ta bort applösenord på sidan applösenord

1. På sidan **applösenord** väljer du **ta bort** bredvid det applösenord som du vill ta bort.

   ![Ta bort ett applösenord](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Välj **Ja** i bekräftelse rutan och välj sedan **Stäng**.

    Lösen ordet för appen har tagits bort.

## <a name="if-your-app-passwords-arent-working-properly"></a>Om dina applösenord inte fungerar som de ska

Kontrol lera att du har angett lösen ordet korrekt. Om du är säker på att du har angett lösen ordet korrekt kan du försöka logga in igen och skapa ett nytt applösenord. Om inget av dessa alternativ löser problemet kan du kontakta din organisations supportavdelning så att de kan ta bort dina befintliga applösenord, vilket gör att du kan skapa helt nya.

## <a name="next-steps"></a>Nästa steg

- [Hantera dina verifierings inställningar i två steg](multi-factor-authentication-end-user-manage-settings.md)

- Prova Microsoft Authenticator- [appen](user-help-auth-app-download-install.md) för att verifiera dina inloggningar med app-meddelanden, i stället för att ta emot texter eller samtal.
