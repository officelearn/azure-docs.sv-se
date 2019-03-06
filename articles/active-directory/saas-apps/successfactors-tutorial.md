---
title: 'Självstudier: Azure Active Directory-integrering med SuccessFactors | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c964d4e070bafc6e8e66535c3963d82803d3cd1e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865781"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Självstudier: Azure Active Directory-integrering med SuccessFactors

I den här självstudien lär du dig att integrera SuccessFactors med Azure Active Directory (AD Azure).
Genom att integrera SuccessFactors med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till SuccessFactors.
* Du kan göra så att dina användare automatiskt loggas in på SuccessFactors (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med SuccessFactors behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SuccessFactors-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SuccessFactors stöder **IDP**-initierad enkel inloggning

## <a name="adding-successfactors-from-the-gallery"></a>Lägga till SuccessFactors från galleriet

För att konfigurera integreringen av SuccessFactors till Azure AD behöver du lägga till SuccessFactors från galleriet till listan över hanterade SaaS-appar.

**Lägg till SuccessFactors från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SuccessFactors**, väljer **SuccessFactors** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![SuccessFactors i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med SuccessFactors baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SuccessFactors upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med SuccessFactors behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SuccessFactors](#configure-successfactors-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SuccessFactors-testanvändare](#create-successfactors-test-user)** – för att ha en motsvarighet för Britta Simon i SuccessFactors som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med SuccessFactors:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **SuccessFactors**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SuccessFactors-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. I textrutan **Identifierare** anger du en URL med följande mönster:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. I textrutan **Svars-URL** anger du en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för SuccessFactors-klienten](https://www.successfactors.com/content/ssf-site/en/support.html).

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SuccessFactors** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-successfactors-single-sign-on"></a>Konfigurera enkel inloggning för SuccessFactors

1. I ett annat webbläsarfönster loggar du in på din **SuccessFactors-administratörsportal** som administratör.

2. Gå till **Application Security** (Programsäkerhet) och sedan till **funktionen för enkel inloggning**.

3. Placera valfritt värde i **Reset Token** (Återställ token) och klicka på **Save Token** (Spara Token) för att aktivera enkel inloggning med SAML.

    ![Konfigurera enkel inloggning på appsidan][11]

    > [!NOTE]
    > Det här värdet används som på/av-växel. Om ett värde sparas är enkel inloggning med SAML PÅ. Om ett tomt värde sparas är enkel inloggning med SAML AV.

4. Gå till skärmbilden nedan och utför följande åtgärder:

    ![Konfigurera enkel inloggning på appsidan][12]
  
    a. Markera alternativknappen **SAML v2 SSO** (Enkel inloggning med SAML v2)
  
    b. Ange **SAML Asserting Party Name** (Namn på försäkrande part för SAML) (till exempel SAML-utfärdare plus företagsnamn).

    c. I textrutan **Issuer URL** (Utfärdar-URL) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    d. Välj **Assertion** (Försäkran) för **Require Mandatory Signature** (Kräv obligatorisk signatur).

    e. Välj **Enabled** (Aktiverad) för **Enable SAML Flag** (Aktivera SAML-flagga).

    f. Välj **No** (Nej) för **Login Request Signature(SF Generated/SP/RP)** (Signatur för inloggningsbegäran (SF-genererad/SP/RP)).

    g. Välj **Browser/Post Profile** (Webbläsar-/postprofil) för **SAML Profile** (SAML-profil).

    h. Välj **No** (Nej) för **Enforce Certificate Valid Period** (Tillämpa giltighetsperiod för certifikat).

    i. Kopiera innehållet i den nedladdade certifikatfilen från Azure-portalen och klistra in det i textrutan **SAML Verifying Certificate** (Verifieringscertifikat för SAML).

    > [!NOTE] 
    > Certifikatinnehållet måste ha starttaggar och sluttaggar för certifikat.

5. Gå till SAML-V2 och utför sedan följande steg:

    ![Konfigurera enkel inloggning på appsidan][13]

    a. Välj **Yes** (Ja) för **Support SP-initiated Global Logout** (Stöd SP-initierad global utloggning).

    b. I textrutan **Global Logout Service URL (LogoutRequest destination)** (Tjänst-URL för global utloggning (LogoutRequest-destination)) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. Välj **No** (Nej) för **Require sp must encrypt all NameID element** (Kräv att SP krypterar alla NameID-element).

    d. Välj **unspecified** (ospecificerad) för **NameID Format** (NameID-format).

    e. Välj **Ja** för **Enable sp initiated login (AuthnRequest)** (Aktivera SP-initierad inloggning (AuthnRequest)).

    f. I textrutan **Send request as Company-Wide issuer** (Skicka begäran som företagsomfattande utfärdare) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

6. Utför de här stegen om du göra användarnamnen för inloggning skiftlägeskänsliga.

    ![Konfigurera enkel inloggning][29]

    a. Gå till **Company Settings** (Företagsinställningar) (nästan längst ned).

    b. Markera kryssrutan nära **Enable Non-Case-Sensitive Username** (Aktivera ej skiftlägeskänsligt användarnamn).

    c. Klicka på **Spara**.

    > [!NOTE]
    > Om du försöker aktivera detta så kontrollerar systemet om det skapa ett duplicerat SAML-inloggningsnamn. Ett exempel kan vara om kunden har användarnamnen User1 och user1. Om skiftlägeskänslighet tas bort blir dessa namn dubbletter. Systemet visar ett felmeddelande och aktiverar inte funktionen. Kunden måste ändra något av användarnamnen så att de stavas olika.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till SuccessFactors.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **SuccessFactors**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SuccessFactors**.

    ![SuccessFactors-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-successfactors-test-user"></a>Skapa SuccessFactors-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på SuccessFactors måste de etableras i SuccessFactors. När det gäller SuccessFactors är etablering en manuell aktivitet.

För att skapa användare i SuccessFactors behöver du kontakta [SuccessFactors-supportteamet](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SuccessFactors-panelen i åtkomstpanelen bör du automatiskt loggas in på SuccessFactors som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
