---
title: 'Självstudier: Azure Active Directory-integrering med Andromeda | Microsoft Docs'
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
ms.openlocfilehash: b2f134e0d655f07fbe9589c96b6ff07c7ee23c92
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698373"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Självstudier: Azure Active Directory-integrering med Andromeda

I den här självstudien får du lära dig hur du integrerar Andromeda med Azure Active Directory (AD Azure).
Integrera Andromeda med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Andromeda.
* Du kan aktivera användarna att vara automatiskt inloggad till Andromeda (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Andromeda, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Andromeda enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Andromeda **SP och IDP** -initierad SSO
* Har stöd för Andromeda **Just In Time** etableringen av användare

## <a name="adding-andromeda-from-the-gallery"></a>Att lägga till Andromeda från galleriet

För att konfigurera integrering av Andromeda i Azure AD, som du behöver lägga till Andromeda från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Andromeda från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Andromeda**väljer **Andromeda** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Andromeda i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Andromeda baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Andromeda upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Andromeda, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Andromeda Single Sign-On](#configure-andromeda-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Andromeda](#create-andromeda-test-user)**  – du har en motsvarighet för Britta Simon i Andromeda som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Andromeda:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Andromeda** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Andromeda domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Andromeda domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du ska uppdatera värdet med faktiska identifierare, svars-URL och inloggnings-URL som beskrivs senare i självstudien.

6. Andromeda program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

    > [!Important]
    > Rensa namnområdesdefinitioner när du konfigurerar dessa.

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn | Källattribut|
    | ------ | -----------|
    | roll        | Ansvarar för att appen |
    | typ        | Apptyp |
    | company       | CompanyName |

    > [!NOTE]
    > Det finns inga verkliga värden. Dessa värden är endast för demo ändamål, Använd roller för din organisation.

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

9. På den **konfigurera Andromeda** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-andromeda-single-sign-on"></a>Konfigurera Andromeda enkel inloggning

1. Inloggning till webbplatsen Andromeda företag som administratör.

2. Överst på menyraden klickar du på **Admin** och gå till **Administration**.

    ![Andromeda admin](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. På vänster sida av verktygsfältet under **gränssnitt** klickar du på **SAML-konfiguration**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. På den **SAML-konfiguration** avsnittet sidan, utför följande steg:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Kontrollera **aktivera enkel inloggning med SAML**.

    b. Under **Andromeda Information** avsnittet, kopiera den **SP identitet** värde och klistra in den i den **identifierare** textrutan av **grundläggandeförSAML-konfiguration** avsnittet.

    c. Kopiera den **konsument URL** värde och klistra in den i den **svars-URL** textrutan av **grundläggande SAML-konfiguration** avsnittet.

    d. Kopiera den **inloggnings-URL** värde och klistra in den i den **inloggnings-URL** textrutan av **SAML grundkonfiguration** avsnittet.

    e. Under **SAML-identitetsprovider** Skriv namnet på din IDP: N.

    f. I den **enkel inloggning på slutpunkten** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    g. Öppna den hämtade **Base64-kodat certifikat** från Azure-portalen i anteckningar, klistra in den i den **X 509-certifikat** textrutan.
    
    h. Mappa följande attribut med respektive värdet att underlätta SSO-inloggning från Azure AD. Den **användar-ID** attributet är obligatoriskt för att logga in. För att etablera, **e-post**, **företagets**, **UserType**, och **rollen** krävs. I det här avsnittet ska definiera vi attribut mappning (namn och värden) som korrelera till de som anges i Azure-portalen

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Andromeda.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Andromeda**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Andromeda**.

    ![Länken Andromeda i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-andromeda-test-user"></a>Skapa Andromeda testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Andromeda. Andromeda stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Andromeda, skapas en ny efter autentisering. Om du vill skapa en användare manuellt kan du kontakta [Andromeda klienten supportteamet](https://www.ngcsoftware.com/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Andromeda i åtkomstpanelen, bör det vara loggas in automatiskt till Andromeda som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)