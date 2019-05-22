---
title: 'Självstudier: Azure Active Directory-katalogintegrering med AlertOps | Microsoft Docs'
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
ms.openlocfilehash: 124f69b3fd6944d0a6e3814f1bbfa5594bcd95cb
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988443"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Självstudier: Integrera AlertOps med Azure Active Directory

I de här självstudierna lär du dig att integrera AlertOps med Azure Active Directory (AD Azure). När du integrerar AlertOps med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till AlertOps.
* Ge dina användare att automatiskt inloggad till AlertOps med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration AlertOps enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för AlertOps **SP och IDP** -initierad SSO.

## <a name="adding-alertops-from-the-gallery"></a>Lägga till AlertOps från galleriet

För att konfigurera integrering av AlertOps i Azure AD behöver du lägga till AlertOps från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **AlertOps** i sökrutan.
1. Välj **AlertOps** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med AlertOps med en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i AlertOps för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med AlertOps, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera AlertOps](#configure-alertops)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare AlertOps](#create-alertops-test-user)**  har en motsvarighet för Britta Simon i AlertOps som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **AlertOps** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    1. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.alertops.com`

    1. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för AlertOps-klienten](mailto:support@alertops.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera AlertOps** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Konfigurera AlertOps

1. Om du vill automatisera konfigurationen inom AlertOps, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet AlertOps** omdirigerar dig till programmet AlertOps. Ange administratörsautentiseringsuppgifter för att logga in på AlertOps därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 5.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera AlertOps manuellt, öppna ett nytt webbläsarfönster och logga in på webbplatsen AlertOps företag som administratör och utför följande steg:

4. Klicka på **Kontoinställningar** från den vänstra navigeringspanelen.

    ![AlertOps-konfiguration](./media/alertops-tutorial/configure1.png)

5. På sidan med **prenumerationsinställningar** väljer du **SSO** och utför följande steg:

    ![AlertOps-konfiguration](./media/alertops-tutorial/configure2.png)

    a. Markera kryssrutan **Use Single Sign-On(SSO)** (Använd enkel inloggning (SSO)).

    b. Välj **Azure Active Directory** som en **SSO-providern** i listrutan.

    c. I den **utfärdar-URL** textrutan använda ID-värde som du har använt i den **SAML grundkonfiguration** avsnitt i Azure-portalen.

    d. I den **slutpunkts-URL för SAML** textrutan klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    e. I den **slutpunkts-URL för Servicenivåmål** textrutan klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    f. Välj **SHA256** som en **SAML-signaturalgoritm** i listrutan.

    g. Öppna den nedladdade Certificate(Base64)-filen i Anteckningar. Kopiera innehållet i den till Urklipp och klistra in det till textrutan X.509-certifikat.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas Britta Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AlertOps.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **AlertOps**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-alertops-test-user"></a>Skapa AlertOps-testanvändare

1. I ett annat webbläsarfönster, loggar du in din AlertOps företagets webbplats som administratör.

2. Klicka på **Användare** från det vänstra navigeringsfönstret.

    ![AlertOps-konfiguration](./media/alertops-tutorial/user1.png)

3. Välj **Lägg till användare**.

    ![AlertOps-konfiguration](./media/alertops-tutorial/user2.png)

4. I dialogrutan **Lägg till användare** utför du följande steg:

    ![AlertOps-konfiguration](./media/alertops-tutorial/user3.png)

    a. I textrutan **Login User Name** (Användarnamn för inloggning) anger du namnet på användaren, t.ex. **Brittasimon**.

    b. I den **officiella e-post** textrutan anger du e-postadressen för användaren som **Brittasimon\@contoso.com**.

    c. I textrutan **Förnamn** skriver du förnamnet på användaren som **Britta**.

    d. I textrutan **Efternamn** skriver du förnamnet på användaren som **Simon**.

    e. Välj **typvärde** i listrutan enligt din organisation.

    f. Välj **roll** för användaren i listrutan enligt din organisation.

    g. Välj **Lägg till**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen AlertOps i åtkomstpanelen, bör det vara loggas in automatiskt till AlertOps som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)