---
title: Aktivera Azure Multi-Factor Authentication
description: I den här självstudien får du lära dig hur du aktiverar Azure Multi-Factor Authentication för en grupp användare och testar den sekundära faktorprompten under en inloggningshändelse.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77154825"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Självstudiekurs: Säkra inloggningshändelser för användare med Azure Multi-Factor Authentication

Multifaktorautentisering (MFA) är en process där en användare uppmanas under en inloggningshändelse för ytterligare former av identifiering. Denna uppmaning kan vara att ange en kod på sin mobiltelefon eller att ge ett fingeravtryck scan. När du behöver en andra form av autentisering ökar säkerheten eftersom den här ytterligare faktorn inte är något som är lätt för en angripare att hämta eller duplicera.

Azure Multi-Factor Authentication och Conditional Access principer ger flexibiliteten att aktivera MFA för användare under specifika inloggningshändelser.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en princip för villkorlig åtkomst för att aktivera Azure Multi-Factor Authentication för en grupp användare
> * Konfigurera principvillkor som frågar efter MFA
> * Testa MFA-processen som användare

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En fungerande Azure AD-klient med Azure AD Premium eller utvärderingslicens aktiverad.
    * Om det behövs, [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med globala administratörsbehörighet. *global administrator*
* En användare som inte är administratör med ett lösenord som du *känner till,* till exempel testanvändare . Du testar slutanvändaren Azure Multi-Factor Authentication erfarenhet med det här kontot i den här självstudien.
    * Om du behöver skapa en användare läser du [Snabbstart: Lägg till nya användare i Azure Active Directory](../add-users-azure-active-directory.md).
* En grupp som användaren som inte är administratör är medlem i, till exempel *MFA-Test-Group*. Du aktiverar Azure Multi-Factor Authentication för den här gruppen i den här självstudien.
    * Om du behöver skapa en grupp kan du se hur du [skapar en grupp och lägger till medlemmar i Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Det rekommenderade sättet att aktivera och använda Azure Multi-Factor Authentication är med principer för villkorlig åtkomst. Med villkorlig åtkomst kan du skapa och definiera principer som reagerar på inloggningshändelser och begär ytterligare åtgärder innan en användare beviljas åtkomst till ett program eller en tjänst.

![Översiktsdiagram över hur villkorlig åtkomst fungerar för att skydda inloggningsprocessen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Principer för villkorlig åtkomst kan vara detaljerade och specifika, med målet att ge användarna möjlighet att vara produktiva var och när som helst, men också skydda din organisation. I den här självstudien ska vi skapa en grundläggande princip för villkorlig åtkomst för att fråga efter MFA när en användare loggar in på Azure-portalen. I en senare självstudiekurs i den här serien konfigurerar du Azure Multi-Factor Authentication med hjälp av en riskbaserad princip för villkorlig åtkomst.

Skapa först en princip för villkorlig åtkomst och tilldela din testgrupp av användare enligt följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto med globala administratörsbehörigheter. *global administrator*
1. Sök efter och välj **Azure Active Directory**och välj sedan **Säkerhet** på menyn till vänster.
1. Välj **Villkorlig åtkomst**och välj sedan + Ny **princip**.
1. Ange ett namn för principen, till exempel *MFA Pilot*.
1. Under **Tilldelningar**väljer du **Användare och grupper**och sedan alternativknappen Välj användare och **grupper.**
1. Markera kryssrutan för **användare och grupper**och **välj** sedan för att bläddra bland tillgängliga Azure AD-användare och -grupper.
1. Bläddra efter och välj din Azure AD-grupp, till exempel *MFA-Test-Group*, och välj sedan **Välj**.

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Om du vill tillämpa principen Villkorlig åtkomst för gruppen väljer du **Klar**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Konfigurera villkoren för multifaktorautentisering

När principen villkorlig åtkomst har skapats och en testgrupp av användare har tilldelats definierar du nu de molnappar eller åtgärder som utlöser principen. Dessa molnappar eller -åtgärder är de scenarier som du bestämmer kräver ytterligare bearbetning, till exempel för att fråga efter MFA. Du kan till exempel bestämma att åtkomst till ett ekonomiskt program eller användning av hanteringsverktyg kräver som en ytterligare verifieringsfråga.

För den här självstudien konfigurerar du principen villkorlig åtkomst så att den kräver MFA när en användare loggar in på Azure-portalen.

1. Välj **Molnappar eller åtgärder**. Du kan välja att tillämpa principen villkorlig åtkomst på *alla molnappar* eller *Välj appar*. Om du vill ge flexibilitet kan du också utesluta vissa appar från principen.

    För den här självstudien väljer du alternativknappen **Välj appar** på sidan *Inkludera.*

1. Välj **Välj**och bläddra sedan i listan över tillgängliga inloggningshändelser som kan användas.

    För den här självstudien väljer du **Microsoft Azure Management** så att principen gäller för inloggningshändelser på Azure-portalen.

1. Om du vill använda de valda apparna väljer du **Välj**och sedan **Gjort**.

    ![Välj den Microsoft Azure Management-app som ska inkluderas i principen villkorlig åtkomst](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Med åtkomstkontroller kan du definiera kraven för en användare som ska beviljas åtkomst, till exempel att behöva en godkänd klientapp eller använda en enhet som hybrid Azure AD gick med. I den här självstudien konfigurerar du åtkomstkontrollerna så att de kräver MFA-funktion under en inloggningshändelse till Azure-portalen.

1. Under *Access-kontroller*väljer du **Bevilja**och kontrollerar sedan att alternativknappen **Bevilja åtkomst** är markerad.
1. Markera kryssrutan **Kräv multifaktorautentisering**och välj sedan **Markera**.

Principer för villkorlig åtkomst kan ställas in *på Endast rapportera* om du vill se hur konfigurationen skulle påverka användare eller *Av* om du inte vill använda principen just nu. Som en testgrupp av användare var riktad för den här självstudien, kan aktivera principen och sedan testa Azure Multi-Factor Autentisering.

1. Ange *växlingsknappen Aktivera* princip till **På**.
1. Om du vill använda principen Villkorlig åtkomst väljer du **Skapa**.

## <a name="test-azure-multi-factor-authentication"></a>Testa Azure Multi-Factor Authentication

Låt oss se din policy för villkorlig åtkomst och Azure Multi-Factor Authentication i praktiken. Logga först in på en resurs som inte kräver MFA enligt följande:

1. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläget och bläddra till[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Logga in med testanvändaren som inte är administratörer, till exempel *testanvändare*. Det finns ingen uppmaning för dig att slutföra MFA.
1. Stäng webbläsarfönstret.

Logga nu in på Azure-portalen. När Azure-portalen har konfigurerats i principen villkorlig åtkomst för att kräva ytterligare verifiering får du en azure multifaktorautentiseringsfråga.

1. Öppna ett nytt webbläsarfönster i InPrivate- eller [https://portal.azure.com](https://portal.azure.com)inkognitoläget och bläddra till .
1. Logga in med testanvändaren som inte är administratörer, till exempel *testanvändare*. Du måste registrera dig för och använda Azure Multi-Factor Authentication. Följ anvisningarna för att slutföra processen och verifiera att du har loggat in på Azure-portalen.

    ![Följ webbläsaruppmaningar och sedan på din registrerade multifaktorautentiseringsfråga för att logga in](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Stäng webbläsarfönstret.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda principen villkorlig åtkomst för att aktivera Azure Multi-Factor Authentication som konfigurerats som en del av den här självstudien tar du bort principen med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory**och välj sedan **Säkerhet** på menyn till vänster.
1. Välj **Villkorlig åtkomst**och välj sedan den princip som du skapade, till exempel *MFA Pilot*
1. Välj **Ta bort**och bekräfta sedan att du vill ta bort principen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat Azure Multi-Factor Authentication med principer för villkorlig åtkomst för en vald grupp användare. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en princip för villkorlig åtkomst för att aktivera Azure Multi-Factor-autentisering för en grupp Azure AD-användare
> * Konfigurera principvillkor som frågar efter MFA
> * Testa MFA-processen som användare

> [!div class="nextstepaction"]
> [Aktivera återställning av lösenord för återställning av lösenord med självbetjäning (SSPR)](tutorial-enable-writeback.md)
