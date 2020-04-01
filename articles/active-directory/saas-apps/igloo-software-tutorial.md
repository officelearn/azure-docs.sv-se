---
title: 'Självstudiekurs: Azure Active Directory-integrering med Igloo Software | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Igloo Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100589"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Självstudiekurs: Azure Active Directory-integrering med Igloo Software

I den här självstudien får du lära dig hur du integrerar Igloo-programvara med Azure Active Directory (Azure AD).
Genom att integrera Igloo-programvara med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Igloo Software.
* Du kan aktivera dina användare automatiskt inloggad på Igloo Software (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Igloo-programvara behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Igloo Software enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Igloo Software stöder **SP** initierade SSO
* Igloo Software stöder **just i tid** användare etablering

## <a name="adding-igloo-software-from-the-gallery"></a>Lägga till Igloo Software från galleriet

Om du vill konfigurera integreringen av Igloo-programvara i Azure AD måste du lägga till Igloo-programvara från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Igloo-programvara från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Igloo Software**i sökrutan och välj **Igloo-programvara** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Igloo Software i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Igloo Software baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Igloo-programvaran upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Igloo-programvara måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Igloo Software Single Sign-On](#configure-igloo-software-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Igloo Software testanvändare](#create-igloo-software-test-user)** - att ha en motsvarighet till Britta Simon i Igloo Software som är kopplad till Azure AD representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Igloo-programvara:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Igloo** **Software-programprogram.**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Igloo Software Domain och webbadresser enkel inloggningsinformation](common/sp-identifier-reply.png)

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<company name>.igloocommmunities.com`

    b. Skriv en URL med följande mönster i rutan **Identifierare:**`https://<company name>.igloocommmunities.com/saml.digest`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Igloo Software Client support team](https://www.igloosoftware.com/services/support) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Igloo-programvara.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-igloo-software-single-sign-on"></a>Konfigurera Igloo Software Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din Igloo Software company-webbplats som administratör.

2. Gå till **Kontrollpanelen**.

     ![Kontrollpanelen](./media/igloo-software-tutorial/ic799949.png "Kontrollpanelen")

3. Klicka på Logga **in inställningar på**fliken **Medlemskap** .

    ![Logga in inställningar](./media/igloo-software-tutorial/ic783968.png "Logga in inställningar")

4. Klicka på **Konfigurera SAML-autentisering**i avsnittet SAML-konfiguration .

    ![SAML-konfiguration](./media/igloo-software-tutorial/ic783969.png "SAML-konfiguration")

5. Gör följande i avsnittet **Allmän konfiguration:**

    ![Allmän konfiguration](./media/igloo-software-tutorial/ic783970.png "Allmän konfiguration")

    a. Skriv ett eget namn för konfigurationen i textrutan **Anslutningsnamn.**

    b. Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan för **IdP-inloggningsadress.**

    c. Klistra in värdet för **url-URL-url för utloggning** som du har kopierat från Azure-portalen i **i idP-url-url-textrutan.**

    d. Välj **Logout Svar och Begär HTTP-typ** som **POST**.

    e. Öppna ditt **bas-64-kodade** certifikat i anteckningar som hämtats från Azure-portalen, kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **offentligt certifikat.**

6. I **svars- och autentiseringskonfigurationen**utför du följande steg:

    ![Konfiguration av svar och autentisering](./media/igloo-software-tutorial/IC783971.png "Konfiguration av svar och autentisering")
  
    a. Som **identitetsprovider**väljer du **Microsoft ADFS**.

    b. Som **identifierare typ**väljer du **E-postadress**. 

    c. Skriv **e-postadress**i textrutan **E-postattribut** .

    d. Skriv **givenname**i textrutan **Förnamnsattribut** .

    e. Skriv **efternamn**i textrutan **För efternamnsattribut** .

7. Utför följande steg för att slutföra konfigurationen:

    ![Skapa användare vid inloggning](./media/igloo-software-tutorial/IC783972.png "Skapa användare vid inloggning") 

    a. När **användaren skapar i Inloggning**väljer du Skapa en ny användare på webbplatsen när de loggar **in**.

    b. När **du loggar in inställningar**väljer du Använd **SAML-knappen på skärmen "Logga in".**

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till Igloo-programvara.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Igloo Software**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Igloo Software**i programlistan .

    ![Länken Igloo Software i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-igloo-software-test-user"></a>Skapa Igloo Software testanvändare

Det finns inget åtgärdsobjekt för att konfigurera användaretablering till Igloo-programvara.  

När en tilldelad användare försöker logga in på Igloo Software med hjälp av åtkomstpanelen kontrollerar Igloo Software om användaren finns.  Om det inte finns något användarkonto tillgängligt ännu skapas det automatiskt av Igloo Software.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Igloo Software på åtkomstpanelen ska du automatiskt loggas in på Igloo-programvaran som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)