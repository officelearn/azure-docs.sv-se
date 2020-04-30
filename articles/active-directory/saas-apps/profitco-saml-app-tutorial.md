---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med SAML-appen Profit.co | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML-appen Profit.co.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770926"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med SAML-appen Profit.co

I den här självstudien får du lära dig hur du integrerar Profit.co SAML-appen med Azure Active Directory (Azure AD). När du integrerar Profit.co SAML-appen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Profit.co SAML-appen.
* Gör det möjligt för användarna att logga in automatiskt till Profit.co SAML-appen med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Profit.co SAML app enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SAML-appen Profit.co stöder IDP initierad SSO.

* När du har konfigurerat Profit.co SAML-appen kan du framtvinga kontroll av sessionen. Detta skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-saml-app-from-the-gallery"></a>Lägg till Profit.co SAML-app från galleriet

Om du vill konfigurera integreringen av Profit.co SAML-appen i Azure AD måste du lägga till Profit.co SAML-appen från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **profit.co SAML app** i sökrutan.
1. Välj **PROFIT.co SAML-app** i resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Konfigurera och testa enkel inloggning med Azure AD för Profit.co SAML-appen

Konfigurera och testa Azure AD SSO med Profit.co SAML-appen genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera upprättar du en länkad relation mellan en Azure AD-användare och den relaterade användaren i SAML-appen Profit.co.

Här följer allmänna steg för att konfigurera och testa Azure AD SSO med Profit.co SAML-appen:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera PROFIT.co SAML app SSO](#configure-profitco-saml-app-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa PROFIT.co SAML app test User](#create-a-profitco-saml-app-test-user)** för att ha en motsvarighet till B. Simon i profit.co SAML-appen. Den här motsvarigheten är länkad till användarens Azure AD-representation.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. Leta upp avsnittet **Hantera** i [Azure Portal](https://portal.azure.com/)på sidan **profit.co SAML app** Application Integration. Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML Page med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan förifyllda i Azure. Användarna måste spara konfigurationen genom att välja knappen **Spara** .

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , väljer du knappen **Kopiera** . Detta kopierar **URL: en för appens Federations-metadata** och sparar den på din dator.

    ![Skärm bild av SAML-signerings certifikat med knappen Kopiera markerad](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory** > **användare** > **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i fältet **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAML-appen Profit.co.

1. I Azure Portal väljer du **företags program** > **alla program**.
1. I listan program väljer du **PROFIT.co SAML app**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av sidan användare och grupper där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare. Välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-profitco-saml-app-sso"></a>Konfigurera Profit.co SAML app SSO

Om du vill konfigurera enkel inloggning på Profit.co SAML-appen måste du skicka URL: en för appens Federations-metadata till [support teamet för SAML-appen för profit.co](mailto:support@profit.co). De konfigurerar den här inställningen att ha SAML SSO-anslutningen korrekt inställd på båda sidorna.

### <a name="create-a-profitco-saml-app-test-user"></a>Skapa en test användare för Profit.co SAML-app

I det här avsnittet skapar du en användare som heter B. Simon i Profit.co SAML-appen. Arbeta med [PROFIT.co SAML app support team](mailto:support@profit.co) för att lägga till användare i profit.co SAML-appens plattform. Du kan inte använda enkel inloggning förrän du har skapat och aktiverat användare.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer Profit.co SAML-appen på åtkomst panelen, bör du loggas in automatiskt på den Profit.co SAML-app som du ställer in SSO för. Mer information finns i [Introduktion till åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Profit.co SAML-appen med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Profit.co SAML-appen med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
