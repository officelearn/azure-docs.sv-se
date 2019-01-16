---
title: 'Självstudier: Azure Active Directory-integrering med Deskradar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Deskradar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: 41846e2e7fd047ca573892e39d960a353bc7fd18
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54080169"
---
# <a name="tutorial-azure-active-directory-integration-with-deskradar"></a>Självstudier: Azure Active Directory-integrering med Deskradar

I den här självstudien lär du dig att integrera Deskradar med Azure Active Directory (AD Azure).
Genom att integrera Deskradar med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Deskradar.
* Du kan göra så att dina användare automatiskt loggas in på Deskradar (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Deskradar behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Deskradar-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Deskradar stöder **IDP**-initierad enkel inloggning

## <a name="add-deskradar-from-the-gallery"></a>Lägga till Deskradar från galleriet

För att konfigurera integreringen av Deskradar till Azure AD behöver du lägga till Deskradar från galleriet till listan över hanterade SaaS-appar.

**Lägg till Deskradar från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Deskradar**, väljer **Deskradar** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Deskradar i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med [programnamn] baserat på en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i [Programnamn] upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med [Programnamn] behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Deskradar](#configure-deskradar-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Deskradar-testanvändare](#create-deskradar-test-user)** – för att ha en motsvarighet för Britta Simon i Deskradar som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med [Programnamn] genom att utföra följande steg:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Deskradar**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Deskradar-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/consume`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/login`

    ![Deskradar-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Ersätt **YOURDOMAIN** med din Deskradar-instansdomän. Hämta dessa värden genom att kontakta [supportteamet för Deskradar-klienten](mailto:support@deskradar.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Deskradar-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn | Källattribut|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.userprincipalname |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**.

    g. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera Deskradar** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-deskradar-single-sign-on"></a>Konfigurera enkel inloggning för Deskradar

1. Logga in på din Deskradar-instans med administratörskontot med hjälp av e-post och lösenord som du definierade när du registrerade dig med en inbjudningslänk.

2. Öppna panelen **Team** genom att klicka på ikonen i sidofältet.

3. Växla till fliken **Autentisering**.

4. På fliken **SAML 2.0** utför du följande steg:

    ![Deskradar-konfiguration](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Aktivera **SAML**-autentiseringsmetoden.

    b. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) anger du värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) anger du värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

5. Öppna den nedladdade **Certifikat (Base64)**-filen med ett redigeringsprogram och kopiera och klistra in innehållet i fältet **Offentligt certifikat** i Deskradar.

    ![Deskradar-konfiguration](./media/deskradar-tutorial/15-paste-certificate.jpg)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Deskradar.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Deskradar**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Deskradar**.

    ![Deskradar-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-deskradar-test-user"></a>Skapa Deskradar-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Deskradar. Kontakta [supportteamet för Deskradar-klienten](mailto:support@deskradar.com) för att lägga till användare i Deskradar-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

**För att använda programmet i SP-initierat läge följer du stegen nedan:**

1. Starta Deskradar-instansen genom att öppna URL:en i webbläsaren: `https://YOURDOMAIN.deskradar.cloud` (ersätt `YOURDOMAIN` med din Deskradar-instansdomän). 
1. Välj **Enterprise Single Sign-On** (Enkel inloggning för företag).

    ![Deskradar](./media/deskradar-tutorial/16-sso-button.jpg)

- Du loggas in på Deskradar direkt om du redan har loggat in på Active Directory.
- Du omdirigeras till inloggningsformuläret för Active Directory om du inte har loggat in ännu. Du kan ange dina autentiseringsuppgifter för företagskontot där för att logga in.
- Du kommer tillbaka till Deskradar och loggas in på Deskradar vid en godkänd inloggning med autentiseringsuppgifterna för ditt företag.

**För att använda programmet i IdP-initierat läge följer du steget nedan:**

När du klickar på Deskradar-panelen i åtkomstpanelen bör du automatiskt loggas in på Deskradar som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
