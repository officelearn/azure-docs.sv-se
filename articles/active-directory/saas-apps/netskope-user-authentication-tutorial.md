---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Netskope Användarautentisering | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netskope User Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9bb71cd-aaf9-4403-aca5-c5a349ec562a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33af932e405552cf9d8f5aaf6d42cbd095607b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74085375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Netskope Användarautentisering

I den här självstudien får du lära dig hur du integrerar Netskope User Authentication med Azure Active Directory (Azure AD). När du integrerar Netskope User Authentication med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netskope-användarautentisering.
* Gör att användarna automatiskt loggas in på Netskope-användarautentisering med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Netskope User Authentication enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Netskope User Authentication stöder **SP och IDP** initierad SSO

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Lägga till Netskope-användarautentisering från galleriet

Om du vill konfigurera integreringen av Netskope User Authentication i Azure AD måste du lägga till Netskope-användarautentisering från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Netskope-användarautentisering** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Netskope User Authentication** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Konfigurera och testa en azure AD-inloggning för netskope-användarautentisering

Konfigurera och testa Azure AD SSO med Netskope User Authentication med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Netskope User Authentication.

Så här konfigurerar och testar du Azure AD SSO med Netskope-användarautentisering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Netskope User Authentication SSO](#configure-netskope-user-authentication-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Netskope User Authentication testanvändare](#create-netskope-user-authentication-test-user)** - att ha en motsvarighet till B.Simon i Netskope användarautentisering som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Netskope User** **Authentication-program**och väljer enkel inloggning .
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<tenantname>.goskope.com/<customer entered string>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Du kommer att få dessa värden förklaras senare i handledningen.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<tenantname>.goskope.com`

    > [!NOTE]
    > URL-värdena för inloggning är inte verkliga. Uppdatera url-värdet för inloggning med den faktiska inloggnings-URL:en. Kontakta [Netskope User Authentication Client support team](mailto:support@netskope.com) för att få inloggnings-URL-värde. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Netskope-användarautentisering.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Netskope User Authentication.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Netskope User Authentication**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurera SSO för användarautentisering för Netskope

1. Öppna en ny flik i webbläsaren och logga in på webbplatsen för netskope-användarautentisering som administratör.

1. Klicka på fliken **Aktiv plattform.**

    ![Konfiguration av autentisering av Netskope-användare](./media/netskope-user-authentication-tutorial/user1.png)

1. Bläddra ned till **VIDAREBEFORDRA PROXY** och välj **SAML**.

    ![Konfiguration av autentisering av Netskope-användare](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Gör följande på sidan **SAML-inställningar:**

    ![Konfiguration av autentisering av Netskope-användare](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Kopiera **VÄRDET FÖR SAML-entitets-ID** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Kopiera **SAML ACS-URL-värdet** och klistra in det i textrutan **Svara URL** i avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen.

1. Klicka på **LÄGG TILL KONTO**.

    ![Konfiguration av autentisering av Netskope-användare](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Gör följande på sidan **Lägg till SAML-konto:**

    ![Konfiguration av autentisering av Netskope-användare](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. Ange namnet som Azure AD i textrutan **NAME.**

    b. I **textrutan FÖR IDP-URL** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    c. I **textrutan IDP ENTITY ID** klistrar du in Azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    d. Öppna den nedladdade metadatafilen i anteckningar, kopiera innehållet i den i Urklipp och klistra sedan in den i **textrutan för IDP-certifikat.**

    e. Klicka på **SPARA**.

### <a name="create-netskope-user-authentication-test-user"></a>Skapa testanvändare för användarautentisering för Netskope

1. Öppna en ny flik i webbläsaren och logga in på webbplatsen för netskope-användarautentisering som administratör.

1. Klicka på fliken **Inställningar** i det vänstra navigeringsfönstret.

    ![Skapa användarskapare för netskope-användare](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Klicka på fliken **Aktiv plattform.**

    ![Skapa användarskapare för netskope-användare](./media/netskope-user-authentication-tutorial/user1.png)

1. Klicka på fliken **Användare.**

    ![Skapa användarskapare för netskope-användare](./media/netskope-user-authentication-tutorial/add-user.png)

1. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Skapa användarskapare för netskope-användare](./media/netskope-user-authentication-tutorial/user-add.png)

1. Ange e-postadressen till den användare som du vill lägga till och klicka på **LÄGG TILL**.

    ![Skapa användarskapare för netskope-användare](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Netskope User Authentication på åtkomstpanelen bör du automatiskt loggas in på den Netskope-användarautentisering som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Netskope-användarautentisering med Azure AD](https://aad.portal.azure.com/)