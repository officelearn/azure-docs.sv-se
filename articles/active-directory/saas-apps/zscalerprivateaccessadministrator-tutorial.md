---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler privat administratör för användaråtkomst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler privat åtkomst administratör.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce75431de24886c038cd2eb4ee7db02d2b6cde31
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563361"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Självstudier: Azure Active Directory-integrering med Zscaler privat administratör för användaråtkomst

Lär dig hur du integrerar Zscaler privat åtkomst administratör med Azure Active Directory (AD Azure) i den här självstudien.
Integrera Zscaler privat åtkomst administratör med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Zscaler privat åtkomst administratör.
* Du kan aktivera användarna att vara automatiskt inloggad till Zscaler privat Access Administrator (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler privat åtkomst Administrator, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Zscaler privat åtkomst administratören enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler privat administratör för användaråtkomst stöder **SP** och **IDP** -initierad SSO

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Lägger till Zscaler privat åtkomst administratör från galleriet

För att konfigurera integrering av Zscaler privat åtkomst administratör i Azure AD, som du behöver lägga till Zscaler privat åtkomst administratören från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zscaler privat åtkomst administratören från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Zscaler privat åtkomst administratören**väljer **Zscaler privat åtkomst administratören** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![Zscaler privat administratör för användaråtkomst i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler privat administratör för användaråtkomst baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Zscaler privat åtkomst administratör ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler privat åtkomst administratör, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler privat åtkomst administratören enkel inloggning](#configure-zscaler-private-access-administrator-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Zscaler privat åtkomst administratören testanvändare](#create-zscaler-private-access-administrator-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler privat åtkomst administratören som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler privat åtkomst administratören:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Zscaler privat åtkomst administratören** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Zscaler privat åtkomst administratören domän och URL: er med enkel inloggning för information](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `idpadminsso`

5.  Om du vill konfigurera programmet i **SP** initierade läge, utföra följande steg:

    ![Zscaler privat åtkomst administratören domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Zscaler administratör för privat klient supportteamet](https://help.zscaler.com/zpa-submit-ticket) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. På den **upp Zscaler privat åtkomst administratören** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurera Zscaler privat åtkomst administratören Single Sign-On

1. I ett annat webbläsarfönster, logga till Zscaler privat åtkomst administratören som administratör.

2. Klicka på överst **Administration** och gå till **AUTENTISERING** avsnittet klickar du på **IdP-konfigurationen**.

    ![Zscaler privat åtkomst administratör admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. I övre högra hörnet, klickar du på **lägga till IdP-konfigurationen**. 

    ![Zscaler privat åtkomst administratören addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. På den **lägga till IdP-konfigurationen** sidan utför följande steg:
 
    ![Zscaler privat åtkomst administratören idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klicka på **Välj fil** att ladda upp den hämta filen Metadata från Azure AD i den **IdP Metadata filöverföring** fält.

    b. Det läser de **IdP metadata** från Azure AD och fylls informationen för fält som visas nedan.

    ![Zscaler privat åtkomst administratören idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Välj **enkel inloggning** som **administratör**.

    d. Välj din domän från **domäner** fält.
    
    e. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Zscaler privat åtkomst administratör.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Zscaler privat åtkomst administratören**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zscaler privat åtkomst administratören**.

    ![Länken Zscaler privat administratör för användaråtkomst i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Skapa testanvändare Zscaler privat administratör för användaråtkomst

De måste etableras i Zscaler privat åtkomst administratören om du vill aktivera Azure AD-användare att logga in till Zscaler privat åtkomst administratör. När det gäller Zscaler privat åtkomst administratören etablering är en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Zscaler administratör för privata företag som administratör.

2. Klicka på överst **Administration** och gå till **AUTENTISERING** avsnittet klickar du på **IdP-konfigurationen**.

    ![Zscaler privat åtkomst administratör admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicka på **administratörer** från vänster sida av menyn.

    ![Administratör för Zscaler privat administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. I övre högra hörnet, klickar du på **Lägg till administratör**:

    ![Administratör för Zscaler privat Lägg till administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. I den **Lägg till administratör** utför följande steg:

    ![Administratör för Zscaler privat Användaradministration](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. I den **användarnamn** textrutan Ange e-postadress för användaren som BrittaSimon@contoso.com.

    b. I den **lösenord** textrutan skriver du lösenordet.

    c. I den **Bekräfta lösenord** textrutan skriver du lösenordet.

    d. Välj **rollen** som **Zscaler privat åtkomst administratören**.

    e. I den **e-post** textrutan Ange e-postadress för användaren som BrittaSimon@contoso.com.

    f. I den **Phone** textrutan anger du telefonnumret.

    g. I den **tidszon** textrutan Välj tidszonen.

    h. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler privat åtkomst administratör i åtkomstpanelen bör du vara loggas in automatiskt till som du ställer in SSO Zscaler privat åtkomst administratören. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

