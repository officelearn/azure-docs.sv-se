---
title: "Azure AD: Återställa ditt lösenord | Microsoft Docs"
description: "Använd lösenordsåterställning via självbetjäning för att få åtkomst till ditt konto igen"
services: active-directory
keywords: Active directory password management, password management, Azure AD self service password reset, SSPR
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9b7735bbe8d3b2ffc78eba8f1489d70a02fe7342
ms.lasthandoff: 04/12/2017


---
# <a name="help-i-forgot-my-password"></a>Hjälp, jag har glömt mitt lösenord

Steg 1 … få inte panik

Om de följande scenarierna gäller dig kan vi hjälpa dig

* Du är osäker på hur du kommer åt ditt konto och kommer inte ihåg ditt lösenord
* Inget lösenord har tilldelats och din administratör skickade dig hit

## <a name="unlock-your-account"></a>Låsa upp mitt konto

Om du ska låsa upp ditt konto följer du stegen nedan. När du ser **Välj ett nytt lösenord** i steg 6 nedan kan du låsa upp eller ändra ditt lösenord så att du får åtkomst till ditt konto.

## <a name="reset-my-password"></a>Återställa mitt lösenord

Följ stegen nedan för att komma åt ditt konto igen.
1. Klicka på länken **Can't access your account?** (Kan du inte komma åt ditt konto?) på inloggningssidan för ditt arbete eller din skola och klicka sedan på **Arbets- eller skolkonto** eller gå direkt till [sidan för lösenordsåterställning](https://passwordreset.microsoftonline.com/)

    ![Can't access your account?][Login] (Kan du inte komma åt ditt konto?)

2. Ange **användar-ID:t** för ditt arbete eller din skola och bevisa att du inte är en robot genom att skriva texten som visas för CAPTCHA-testet och klicka sedan på **Nästa**
3. Beroende på hur din administratör har konfigurerat inställningarna visas något eller flera av följande:
    * **Skicka e-post till min alternativa e-postadress** – Skickar ett e-postmeddelande med en 6-siffrig kod till din alternativa e-postadress eller till din e-postadress för autentisering (du väljer).
    * **Skicka ett SMS till min mobiltelefon** – Skickar ett SMS med en 6-siffrig kod till din mobiltelefon eller till din telefon för autentisering (du väljer).
    * **Ring min mobiltelefon** – Ringer till din mobiltelefon eller till din telefon för autentisering (du väljer). Tryck på #-knappen för att bekräfta samtalet.
    * **Ring min arbetstelefon** – Ringer din arbetstelefon. Tryck på #-knappen för att bekräfta samtalet.
    * **Svara på mina säkerhetsfrågor** – Visar dina i förväg registrerade säkerhetsfrågor som du måste besvara.
4. Fyll i de obligatoriska fälten med svaren på frågorna och klicka på **Nästa**

    ![Kontrollera dina autentiseringsdata][Verification]

5. Administratören kan kräva ytterligare ett verifieringssteg och du kan behöva upprepa steg 4 igen med ett annat alternativ
6. På sidan **Välj ett nytt lösenord** anger du ett nytt lösenord som uppfyller kraven för din organisation, bekräftar ditt lösenord och klickar sedan på **Slutför**

    ![Ändra ditt lösenord][Change]

7. När du ser **Ditt lösenord har återställts** kan du logga in med ditt nya lösenord.

    ![Ditt lösenord har återställts][Complete]

> [!NOTE]
> Om administratören inte har aktiverat den här funktionen visas länken ”kontakta administratören”. Klicka på länken så kan administratören hjälpa dig via e-post eller en webbportal.
>

När du har använt den här metoden för att låsa upp eller återställa ditt lösenord kanske du får ett e-postmeddelande som bekräftar att processen har slutförts. Meddelandet kommer från ett konto med ett namn som ”Microsoft för din organisations räkning”. Om du får ett e-postmeddelande som detta och inte använde lösenordsåterställning via självbetjäning för att få åtkomst till ditt konto kontaktar du administratören.

## <a name="change-my-password"></a>Ändra mitt lösenord

Om du redan vet ditt lösenord och behöver ändra det provar du stegen nedan

### <a name="change-your-password-from-the-office-365-portal"></a>Ändra ditt lösenord från Office 365-portalen

1. Klicka på din profil längst upp till höger och klicka sedan på **Visa konto**
2. **Säkerhet och sekretess**
3. **Lösenord**
4. Ange ditt gamla lösenord och ange och bekräfta det nya lösenordet
5. **Skicka**

### <a name="change-your-password-from-the-azure-access-panel"></a>Ändra ditt lösenord från Azures åtkomstpanel

1. Logga in på [åtkomstportalen i Azure](https://myapps.microsoft.com/) med ditt befintliga lösenord
2. Klicka på din profil längst upp till höger och klicka sedan på **Profil**
3. **Ändra lösenord**
4. Ange ditt gamla lösenord och ange och bekräfta det nya lösenordet
5. **Skicka**

## <a name="next-steps"></a>Nästa steg

* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md)
* [Registreringssida för lösenordsåterställning](http://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Inloggningssida – Kan du inte komma åt ditt konto?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Kontrollera dina autentiseringsdata"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Ändra ditt lösenord"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Ditt lösenord har återställts"

