---
title: 'Snabbstart: Självåterställning av lösenord med Azure AD'
description: I den här snabbstarten konfigurerar du snabbt självåterställning av lösenord med Azure AD så att användarna kan återställa sina egna lösenord
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58e3254d499e013dc686bf6b7d53f919a457c901
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414076"
---
# <a name="quickstart-self-service-password-reset"></a>Snabbstart: Återställning av lösenord för självbetjäning

I den här snabbstarten går du igenom konfiguration av självåterställning av lösenord (SSPR) som ett enkelt sätt för IT-administratörer att göra så att användarna kan återställa sina lösenord eller låsa upp sina konton.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens.
* Ett konto med behörigheter som global administratör.
* En testanvändare som inte är administratör med ett lösenord som du känner till. Om du behöver skapa en användare finns information i artikeln [Snabbstart: Lägga till nya användare i Azure Active Directory](../add-users-azure-active-directory.md).
* En pilotgrupp att testa med som testanvändaren som inte är administratör är medlem i. Om du behöver skapa en grupp finns information i artikeln [Skapa en grupp och lägga till medlemmar i Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. På **Azure-portalen** väljer du **Återställning av lösenord** under **Azure Active Directory** från din befintliga Azure AD-klientorganisation.

2. På sidan **Egenskaper** går du till alternativet **Self Service Password Reset Enabled** (Självåterställning av lösenord aktiverat) och väljer **Vald**.
    * Från **Välj grupp** väljer du den pilotgrupp som skapades i avsnittet om förutsättningar i den här artikeln.
    * Klicka på **Spara**.

3. Från sidan **Autentiseringsmetoder** gör du följande val:
   * Antal metoder som krävs för återställning: **1**
   * Metoder som är tillgängliga för användare:
      * **E-post**
      * **Kod för mobilapp (förhandsversion)**
   * Klicka på **Spara**.

     ![Val av autentiseringsmetoder för SSPR][Authentication]

4. Från sidan **Registrering** gör du följande val:
   * Kräv att användare registrerar sig vid inloggning: **Ja**
   * Ange antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation: **365**

## <a name="test-self-service-password-reset"></a>Testa självåterställning av lösenord

Nu testar vi din SSPR-konfiguration med en testanvändare. Eftersom Microsoft tillämpar starka autentiseringskrav för administratörskonton i Azure kan det hända att resultatet ändras efter testning med administratörskonto. Mer information om lösenordsprinciper för administratörer finns i vår artikel om [lösenordsprinciper](concept-sspr-policy.md).

1. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge och gå till [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Logga in med en testanvändare som inte är administratör och registrera din autentiseringstelefon.
3. När det är klart klickar du på den knapp som är markerad som **ser bra ut** och stänger webbläsarfönstret.
4. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge och gå till [https://aka.ms/sspr](https://aka.ms/sspr).
5. Ange dina vanliga testanvändares användar-ID och tecknen från CAPTCHA och klicka sedan på **Nästa**.
6. Följ verifieringsstegen för att återställa lösenordet

## <a name="clean-up-resources"></a>Rensa resurser

Det är enkelt att inaktivera lösenordsåterställning via självbetjäning. Öppna Azure AD-klienten och gå till **Egenskaper** > **Återställning av lösenord**. Välj sedan **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du snabbt ställer in självåterställning av lösenord för endast molnanvändarna. Om du vill ta reda på hur du utför en mer detaljerad lansering fortsätter du till lanseringsguiden.

> [!div class="nextstepaction"]
> [Lansera självåterställning av lösenord](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Azure AD-autentiseringsmetoder som är tillgängliga och den kvantitet som krävs"
