---
title: 'Självstudiekurs: Azure Active Directory-integrering med IMPAC Risk Manager | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IMPAC Risk Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c2c153d8e67ff952add177d8a98b7561c3165774
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100388"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Självstudiekurs: Azure Active Directory-integrering med IMPAC Risk Manager

I den här självstudien får du lära dig hur du integrerar IMPAC Risk Manager med Azure Active Directory (Azure AD).
Genom att integrera IMPAC Risk Manager med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till IMPAC Risk Manager.
* Du kan aktivera dina användare automatiskt inloggad på IMPAC Risk Manager (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med IMPAC Risk Manager behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* IMPAC Risk Manager enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* IMPAC Risk Manager stöder **SP och IDP** initierade SSO

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Lägga till IMPAC Risk Manager från galleriet

Om du vill konfigurera integreringen av IMPAC Risk Manager i Azure AD måste du lägga till IMPAC Risk Manager från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till IMPAC Risk Manager från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **IMPAC Risk Manager**i sökrutan och välj **IMPAC Risk Manager** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

    ![IMPAC Risk Manager i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med IMPAC Risk Manager baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i IMPAC Risk Manager upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med IMPAC Risk Manager måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera IMPAC Risk Manager Single Sign-On](#configure-impac-risk-manager-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa IMPAC Risk Manager-testanvändare](#create-impac-risk-manager-test-user)** – om du vill ha en motsvarighet till Britta Simon i IMPAC Risk Manager som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med IMPAC Risk Manager:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **IMPAC Risk** **Manager-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![IMPAC Risk Manager Domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv ett värde som tillhandahålls av IMPAC i textrutan **Identifierare**

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | Miljö | URL-mönster |
    | ---------------|--------------- |
    | För produktion |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För mellanlagring och utbildning  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För utveckling  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För QA |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För test |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | | |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![IMPAC Risk Manager Domän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    | Miljö | URL-mönster |
    | ---------------|--------------- |
    | För produktion |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För mellanlagring och utbildning  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För utveckling  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För QA |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För test |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [IMPAC Risk Manager Client support team](mailto:rmsupport@Impac.co.nz) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera IMPAC Risk Manager.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-impac-risk-manager-single-sign-on"></a>Konfigurera enkel inloggning för IMPAC-riskhanterare

Om du vill konfigurera enkel inloggning på **IMPAC Risk Manager-sidan** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [IMPAC Risk Manager-supportteamet](mailto:rmsupport@Impac.co.nz). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till IMPAC Risk Manager.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **IMPAC Risk Manager**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **IMPAC Risk Manager**i programlistan .

    ![LÄNKEN IMPAC Risk Manager i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-impac-risk-manager-test-user"></a>Skapa IMPAC Risk Manager-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i IMPAC Risk Manager. Arbeta med [IMPAC Risk Manager supportteam](mailto:rmsupport@Impac.co.nz) för att lägga till användarna i IMPAC Risk Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen IMPAC Risk Manager på åtkomstpanelen bör du automatiskt loggas in på den IMPAC-riskhanterare som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
