---
title: Konfigurera en en klickar du på SSO till ditt program från Azure AD-appgalleri | Microsoft Docs
description: Steg för att konfigurera en klickar du på enkel inloggning i ditt program från Azure AD-appgalleri.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057850"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>En funktion för klickar du på enkel inloggning för Azure AD-Galleriprogram

 I den här självstudien får du lära dig hur du utför en klickar du på enkel inloggning för alla SAML-program som tillhandahåller Användargränssnittet för konfiguration av enkel inloggning.

## <a name="introduction-to-one-click-sso"></a>Introduktion till ett enda klick SSO

En klickar du på SSO-funktionen är implementerad om du vill konfigurera den enkel inloggning för Azure AD-galleriet-appar som har stöd för SAML-protokoll. På sidan för konfiguration av enkel inloggning för Azure AD har vi lagt till det här alternativet så att våra kunder att konfigurera Azure AD-metadata på programsidan automatiskt. Målet är att hjälpa kunder att konfigurera enkel inloggning snabbt med minimalt manuellt arbete. 

## <a name="advantages-of-the-one-click-sso"></a>Fördelarna med det klickar du på enkel inloggning

- Snabb SSO-konfiguration av galleriprogram där kunderna måste göra manuell installation på programsidan.
- Mer effektivt sätt av konfigurationen.
- Ingen partner kommunikation eller stöd för installationsprogrammet som programmet tillhandahåller Användargränssnittet för SAML-konfiguration.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Aktiv prenumeration av program med administratörsautentiseringsuppgifter som du vill konfigurera med OneClick SSO.
- **Mina appar skyddat inloggning webbläsartillägget** från Microsoft som installerats i webbläsaren. Om du vill veta mer om det här tillägget kan referera till denna [länk](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-feature-step-by-step-details"></a>En klickar du på SSO funktionen steg för steg-information

1. Lägg till programmet från Azure AD-appgalleriet.

2. Klicka på enkel inloggning.

3. Klicka på Aktivera enkel inloggning.

4. Fyll i obligatoriska konfigurationsvärdena i avsnittet grundläggande SAML-konfiguration.

    > [!NOTE] 
    > Om programmet behöver konfigurationen av anpassade anspråk, konfigurera dem innan du utför OneClick SSO.

5. Om en klickar du på SSO-funktionen används för alla galleriprogram, visas följande skärm. Om den **Mina appar skyddat inloggning webbläsartillägget** är inte redan är installerat måste du klicka på **installera tillägget** alternativet.

    ![Installera Mina appar skyddat inloggning webbläsartillägg](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. När du lägger till tillägget till webbläsaren, klickar du på **installationsprogrammet programnamn** som kommer att omdirigera dig till program-administratörsportalen. Du måste logga in som administratör komma in i programmet.

    ![Konfigurera programnamn](./media/one-click-sso-tutorial/setup-sso.png)

7. Webbläsartillägget nu konfigurerar automatiskt programmet åt dig. Först uppmanas du att ange din bekräftelse om du vill fortsätta. Klicka på **Ja**.

    ![Spara automatiskt ifylld data](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > Om alla program behöver extra nagivation eller steg, bör du se rätt meddelanden som uppmanar dig att utföra de här stegen. 

8. När konfigurationen är klar klickar du på **Ok** att spara ändringarna.

    ![Spara data fylls i automatiskt](./media/one-click-sso-tutorial/save-data.png)

9. Ett popup-meddelande för lyckade bekräftelse visas och SSO-inställningar angetts har. Du kan sedan testa programmet.

    ![Enkel inloggning konfigurerad](./media/one-click-sso-tutorial/sso-configured.png)

10. När konfigurationen är slutförd måste programmet kommer att loggas ut och du kommer tillbaka till Azure-portalen.

11. Du kan klicka på knappen Testa att testa den enkel inloggning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Vad är Mina appar skyddat inloggning webbläsartillägget](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 