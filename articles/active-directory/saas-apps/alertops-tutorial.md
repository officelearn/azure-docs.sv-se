---
title: 'Självstudiekurs: Azure Active Directory-integrering med AlertOps | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107061"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Självstudiekurs: Integrera AlertOps med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar AlertOps med Azure Active Directory (Azure AD). När du integrerar AlertOps med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AlertOps.
* Gör att användarna automatiskt loggas in på AlertOps med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* AlertOps enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. AlertOps stöder **SP och IDP** initierade SSO.

## <a name="adding-alertops-from-the-gallery"></a>Lägga till AlertOps från galleriet

För att konfigurera integrering av AlertOps i Azure AD behöver du lägga till AlertOps från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **AlertOps** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **AlertOps** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med AlertOps med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i AlertOps.

Om du vill konfigurera och testa Azure AD SSO med AlertOps slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera AlertOps](#configure-alertops)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** så att Britta Simon kan använda azure AD-enkel inloggning.
5. **[Skapa AlertOps-testanvändare](#create-alertops-test-user)** om du vill ha en motsvarighet till Britta Simon i AlertOps som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **AlertOps-program** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    1. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<SUBDOMAIN>.alertops.com`

    1. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för AlertOps-klienten](mailto:support@alertops.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera AlertOps.**

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Konfigurera AlertOps

1. Om du vill automatisera konfigurationen i AlertOps måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Setup AlertOps** som leder dig till AlertOps-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på AlertOps. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera AlertOps manuellt öppnar du ett nytt webbläsarfönster och loggar in på din AlertOps-företagswebbplats som administratör och utför följande steg:

4. Klicka på **Kontoinställningar** från den vänstra navigeringspanelen.

    ![AlertOps-konfiguration](./media/alertops-tutorial/configure1.png)

5. På sidan med **prenumerationsinställningar** väljer du **SSO** och utför följande steg:

    ![AlertOps-konfiguration](./media/alertops-tutorial/configure2.png)

    a. Markera kryssrutan **Use Single Sign-On(SSO)** (Använd enkel inloggning (SSO)).

    b. Välj **Azure Active Directory** som **SSO-leverantör** i listrutan.

    c. I textrutan **Utfärdare URL** använder du identifierarevärdet, som du har använt i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    d. I **SMS-textrutan FÖR SAML-slutpunkts-URL** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    e. I textrutan **URL för SLO-slutpunkt** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    f. Välj **SHA256** som en **SAML-signaturalgoritm** i listrutan.

    g. Öppna den nedladdade Certificate(Base64)-filen i Anteckningar. Kopiera innehållet i den till Urklipp och klistra in det till textrutan X.509-certifikat.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till AlertOps.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I listan med program väljer **AlertOps**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-alertops-test-user"></a>Skapa AlertOps-testanvändare

1. I ett annat webbläsarfönster loggar du in på din AlertOps-företagswebbplats som administratör.

2. Klicka på **Användare** från det vänstra navigeringsfönstret.

    ![AlertOps-konfiguration](./media/alertops-tutorial/user1.png)

3. Välj **Lägg till användare**.

    ![AlertOps-konfiguration](./media/alertops-tutorial/user2.png)

4. I dialogrutan **Lägg till användare** utför du följande steg:

    ![AlertOps-konfiguration](./media/alertops-tutorial/user3.png)

    a. I textrutan **Login User Name** (Användarnamn för inloggning) anger du namnet på användaren, t.ex. **Brittasimon**.

    b. I den **officiella e-posttextrutan** anger du e-postadressen till användaren som **\@Brittasimon contoso.com**.

    c. I textrutan **Förnamn** skriver du förnamnet på användaren som **Britta**.

    d. I textrutan **Efternamn** skriver du förnamnet på användaren som **Simon**.

    e. Välj **typvärde** i listrutan enligt din organisation.

    f. Välj **roll** för användaren i listrutan enligt din organisation.

    g. Välj **Lägg till**.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen AlertOps på åtkomstpanelen bör du automatiskt loggas in på de AlertOps som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)