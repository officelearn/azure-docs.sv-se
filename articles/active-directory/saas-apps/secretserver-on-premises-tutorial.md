---
title: 'Självstudiekurs: Azure Active Directory-integrering med Secret Server (Lokalt) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Secret Server (Lokalt).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4926fc1833cc14b2ad81a01e230a5c3c37ba6ab3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880138"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Självstudiekurs: Integrera hemlig server (lokalt) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Secret Server (Lokalt) med Azure Active Directory (Azure AD). När du integrerar Secret Server (Lokalt) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Secret Server (Lokalt).
* Aktivera användarna automatiskt inloggad på Secret Server (Lokalt) med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Secret Server (Lokalt) enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Secret Server (Lokalt) stöder **SP och IDP** initierad SSO

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Lägga till hemlig server (lokalt) från galleriet

Om du vill konfigurera integreringen av Secret Server (Lokalt) i Azure AD måste du lägga till Secret Server (Lokalt) från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Secret Server (Lokalt)** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Hemlig server (lokalt)** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Secret Server (Lokalt) med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Secret Server (Lokalt).

Så här konfigurerar och testar du Azure AD SSO med Secret Server (Lokalt) slutför följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera hemlig server (lokalt) SSO](#configure-secret-server-on-premises-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa secret server -användare (lokalt)](#create-secret-server-on-premises-test-user)** – om du vill ha en motsvarighet till B.Simon i hemliga server (lokalt) som är länkad till Azure AD-representationen för användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Manage** [Azure-portalen](https://portal.azure.com/)och välj Enkel inloggning på sidan **Hemlig server (lokalt)** program och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare** anger du det användarinvalda värdet som ett exempel:`https://secretserveronpremises.azure`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Enhets-ID som visas ovan är endast ett exempel och du kan välja ett unikt värde som identifierar din Secret Server-instans i Azure AD. Du måste skicka det här entitets-ID:t till [Secret Server (Lokalt) klientsupportteam](https://thycotic.force.com/support/s/) och de konfigurerar det på sin sida. För mer information, läs [den här artikeln](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Secret Server (lokalt) klientsupportteam](https://thycotic.force.com/support/s/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. På sidan Konfigurera enkel inloggning med SAML klickar du på ikonen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat.** **Set up Single Sign-On with SAML**

    ![Signeringsalternativ](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Välj **Signeringsalternativ** som **Signera SAML-svar och påstående**.

    ![Signeringsalternativ](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera hemlig server (lokalt).**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>Konfigurera hemlig server (lokalt) SSO

Om du vill konfigurera enkel inloggning på den **hemliga serversidan (lokalt)** måste du skicka det hämtade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [supportteamet för den hemliga servern (lokalt).](https://thycotic.force.com/support/s/) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Secret Server (Lokalt).

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Secret Server **(Lokalt)** i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-secret-server-on-premises-test-user"></a>Skapa hemlig server (lokalt) testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Secret Server (Lokalt). Arbeta med [Supportteamet för Secret Server (Lokalt)](https://thycotic.force.com/support/s/) för att lägga till användarna på plattformen Secret Server (Lokalt). Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Hemlig server (Lokalt) på åtkomstpanelen ska du automatiskt loggas in på den hemliga server (lokalt) som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)