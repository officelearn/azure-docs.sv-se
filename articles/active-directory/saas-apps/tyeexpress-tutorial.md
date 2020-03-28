---
title: 'Självstudiekurs: Azure Active Directory-integrering med T&E Express | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och T&E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 10ebe796f5aed827a4934a94d568aedbef704503
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088218"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Självstudiekurs: Azure Active Directory-integrering med T&E Express

I den här självstudien får du lära dig hur du integrerar T&E Express med Azure Active Directory (Azure AD).
Genom att integrera T&E Express med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till T&E Express.
* Du kan aktivera dina användare automatiskt inloggad på T&E Express (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med T&E Express behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* T&E Express enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* T&E Express stöder **IDP-initierad** SSO

## <a name="adding-te-express-from-the-gallery"></a>Lägga till T&E Express från galleriet

Om du vill konfigurera integreringen av T&E Express i Azure AD måste du lägga till T&E Express från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till T&E Express från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **T&E Express**, väljer T&E **Express** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

    ![T&E Express i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med T&E Express baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i T&E Express upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med T&E Express måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera T&E Express Enkel inloggning](#configure-te-express-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa T&E Express-testanvändare](#create-te-express-test-user)** – om du vill ha en motsvarighet till Britta Simon i T&E Express som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med T&E Express:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan T&E **Express-programintegration**. **T&E Express**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![T&E Express-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv värdet som URL i textrutan **Identifierare** med följande mönster:`https://<domain>.tyeexpress.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Kontakta [T&E Express Client support team](https://www.tyeexpress.com/contacto.aspx) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera T&E Express.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-te-express-single-sign-on"></a>Konfigurera T&E Express enkel inloggning

1. Om du vill konfigurera enkel inloggning på **T-&E Express-sidan** loggar du in på T&E express-programmet utan SAML-enda inloggning med administratörsautentiseringsuppgifter.

1. Under **fliken Admin** klickar du på **SAML-domänen** för att öppna sidan SAML-inställningar.

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tye-SAML.png)

1. Välj alternativet **Activar(Activate)** från **Nej** till **SI(Ja)**. Klistra in metadata-XML som du har hämtat från Azure-portalen i textrutan **Identitetsprovidermetadata.**

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Klicka på **Guardar (Save)** knappen för att spara inställningarna.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till T&E Express.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan T&**E Express**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj T&**E Express**i programlistan .

    ![T-&E Express-länken i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-te-express-test-user"></a>Skapa T&E Express-testanvändare

För att Azure AD-användare ska kunna logga in på T&E Express måste de etableras i T&E Express. När det gäller T&E Express är etablering en manuell uppgift.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på T&E Express-företagswebbplats som administratör.

1. Under Admin tagg, klicka på Användare för att öppna användarnas huvudsida.

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Klicka på hemsidan för **+** att lägga till användarna på startsidan.

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-usershome.png)

1. Ange alla obligatoriska uppgifter som efterfrågas i formuläret och klicka på spara-knappen för att spara informationen.

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-userssave.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen T&E Express på åtkomstpanelen ska du automatiskt loggas in på den T&E Express som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

