---
title: 'Självstudiekurs: Azure Active Directory-integrering med Andromeda | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68fa570ecfbafe2000bfa6eb9fa159dff48219a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107072"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Självstudiekurs: Azure Active Directory-integrering med Andromeda

I den här självstudien får du lära dig hur du integrerar Andromeda med Azure Active Directory (Azure AD).
Genom att integrera Andromeda med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Andromeda.
* Du kan aktivera dina användare så att de automatiskt loggas in på Andromeda (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Andromeda behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Andromeda enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Andromeda stöder **SP och IDP** initierade SSO
* Andromeda stöder **just in time-användares** etablering

## <a name="adding-andromeda-from-the-gallery"></a>Lägga till Andromeda från galleriet

Om du vill konfigurera integreringen av Andromeda i Azure AD måste du lägga till Andromeda från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Andromeda från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Andromeda**i sökrutan och välj **Andromeda** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Andromeda i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Andromeda baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Andromeda upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Andromeda måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Andromeda Single Sign-On](#configure-andromeda-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Andromeda testanvändare](#create-andromeda-test-user)** - att ha en motsvarighet till Britta Simon i Andromeda som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Andromeda:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Andromeda-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Andromeda Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<tenantURL>.ngcxpress.com/`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Andromeda Domän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du uppdaterar värdet med den faktiska url:en för identifierare, svar och inloggnings-URL som förklaras senare i självstudien.

6. Andromeda ansökan förväntar sig SAML påståenden i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

    > [!Important]
    > Rensa namespace-definitionerna när du konfigurerar dessa.

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn | Källattribut|
    | ------ | -----------|
    | roll        | Appspecifik roll |
    | typ        | Apptyp |
    | company       | CompanyName |

    > [!NOTE]
    > Det finns inga verkliga värden. Dessa värden är endast för demoändamål, använd dina organisationsroller.

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Andromeda.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-andromeda-single-sign-on"></a>Konfigurera andromeda enkel inloggning

1. Logga in på din Andromeda-företagswebbplats som administratör.

2. Högst upp i menyraden klickar du på **Admin** och navigerar till **Administration**.

    ![Andromeda admin](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Klicka på **SAML-konfiguration**till vänster i **verktygsraden** under avsnittet Gränssnitt .

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Gör följande på sidan **SAML-konfiguration:**

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Kontrollera **Aktivera SSO med SAML**.

    b. Under avsnittet **Andromeda-information** kopierar du **SP-identitetsvärdet** och klistrar in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration.**

    c. Kopiera **värdet för konsument-URL** och klistra in det i textrutan **Svara url** i avsnittet **Grundläggande SAML-konfiguration.**

    d. Kopiera **url-värdet för inloggning** och klistra in det i textrutan **Sign-on URL i** avsnittet Grundläggande **SAML-konfiguration.**

    e. Skriv ditt IDP-namn under avsnittet **SAML-identitetsprovider.**

    f. I textrutan **Enkel inloggning vid slutpunkt** klistrar du in värdet **för inloggnings-URL: er** som du har kopierat från Azure-portalen.

    g. Öppna det hämtade **Base64-kodade certifikatet** från Azure-portalen i anteckningar, klistra in det i textrutan **X 509-certifikat.**
    
    h. Mappa följande attribut med respektive värde för att underlätta SSO-inloggning från Azure AD. **Attributet User ID** krävs för inloggning. För etablering krävs **e-post,** **företag,** **UserType**och **roll.** I det här avsnittet definierar vi attributmappning (namn och värden) som korrelerar med dem som definierats i Azure-portalen

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Andromeda.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Andromeda**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Andromeda**i programlistan .

    ![Länken Andromeda i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-andromeda-test-user"></a>Skapa Andromeda-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Andromeda. Andromeda stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Andromeda skapas en ny efter autentisering. Om du behöver skapa en användare manuellt kontaktar du [Supportteamet för Andromeda-klienten](https://www.ngcsoftware.com/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Andromeda på åtkomstpanelen ska du automatiskt loggas in på den Andromeda som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)