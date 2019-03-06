---
title: 'Självstudier: Azure Active Directory-katalogintegrering med AlertOps | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1874950e733df1cf14fba2f53fdb49d478cb6f8a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866053"
---
# <a name="tutorial-azure-active-directory-integration-with-alertops"></a>Självstudier: Azure Active Directory-katalogintegrering med AlertOps

I den här självstudien lär du dig att integrera AlertOps med Azure Active Directory (AD Azure).
När du integrerar AlertOps med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till AlertOps.
* Du kan göra så att dina användare automatiskt loggas in på AlertOps (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med AlertOps behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* AlertOps-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* AlertOps stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-alertops-from-the-gallery"></a>Lägga till AlertOps från galleriet

För att konfigurera integrering av AlertOps i Azure AD behöver du lägga till AlertOps från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AlertOps från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **AlertOps**, väljer **AlertOps** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![AlertOps i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med AlertOps baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i AlertOps upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med AlertOps måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för AlertOps](#configure-alertops-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa AlertOps-testanvändare](#create-alertops-test-user)** – för att ha en motsvarighet för Britta Simon i AlertOps som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med AlertOps:

1. Välj **Enkel inloggning** på sidan för programintegrering av **AlertOps-portalen** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Information om enkel inloggning med AlertOps-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.alertops.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.alertops.com/login.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med AlertOps-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för AlertOps-klienten](mailto:support@alertops.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera AlertOps** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-alertops-single-sign-on"></a>Konfigurera enkel inloggning för AlertOps

1. Öppna ett nytt webbläsarfönster och logga in på din AlertOps-företagswebbplats som administratör.

2. Klicka på **Kontoinställningar** från den vänstra navigeringspanelen.

    ![AlertOps-konfiguration](./media/alertops-tutorial/configure1.png)

3. På sidan med **prenumerationsinställningar** väljer du **SSO** och utför följande steg:

    ![AlertOps-konfiguration](./media/alertops-tutorial/configure2.png)

    a. Markera kryssrutan **Use Single Sign-On(SSO)** (Använd enkel inloggning (SSO)).

    b. Välj **Azure Active Directory** som en **SSO-provider** i listrutan.

    c. I textrutan **Utgivar-URL** använder du identifierarvärdet som du har använt i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    d. I textrutan **SAML endpoint URL** (URL för SAML-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    e. I textrutan **SLO endpoint URL** (URL för SLO-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    f. Välj **SHA256** som en **SAML-signaturalgoritm** i listrutan.

    g. Öppna den nedladdade Certificate(Base64)-filen i Anteckningar. Kopiera innehållet i den till Urklipp och klistra in det till textrutan X.509-certifikat.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till AlertOps.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **AlertOps**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **AlertOps**.

    ![Länken AlertOps i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-alertops-test-user"></a>Skapa AlertOps-testanvändare

1. Öppna ett nytt webbläsarfönster och logga in på din AlertOps-företagswebbplats som administratör.

2. Klicka på **Användare** från det vänstra navigeringsfönstret.

    ![AlertOps-konfiguration](./media/alertops-tutorial/user1.png)

3. Välj **Lägg till användare**.

    ![AlertOps-konfiguration](./media/alertops-tutorial/user2.png)

4. I dialogrutan **Lägg till användare** utför du följande steg:

    ![AlertOps-konfiguration](./media/alertops-tutorial/user3.png)

    a. I textrutan **Login User Name** (Användarnamn för inloggning) anger du namnet på användaren, t.ex. **Brittasimon**.

    b. I textrutan **Official Email** (Officiell e-postadress) anger du e-postadressen för användaren som **Brittasimon@contoso.com**.

    c. I textrutan **Förnamn** skriver du förnamnet på användaren som **Britta**.

    d. I textrutan **Efternamn** skriver du förnamnet på användaren som **Simon**.

    e. Välj **typvärde** i listrutan enligt din organisation.

    f. Välj **roll** för användaren i listrutan enligt din organisation.

    g. Välj **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på AlertOps-panelen i åtkomstpanelen bör du automatiskt loggas in på AlertOps som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
