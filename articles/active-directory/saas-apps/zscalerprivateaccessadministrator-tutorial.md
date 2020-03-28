---
title: 'Självstudiekurs: Azure Active Directory-integrering med Zscaler Private Access Administrator | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Private Access Administrator.
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
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085649"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Självstudiekurs: Azure Active Directory-integrering med Zscaler Private Access Administrator

I den här självstudien får du lära dig hur du integrerar Zscaler Private Access Administrator med Azure Active Directory (Azure AD).
Genom att integrera Zscaler Private Access Administrator med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Zscaler Private Access Administrator.
* Du kan aktivera dina användare så att de automatiskt loggas in på Zscaler Private Access Administrator (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Zscaler Private Access Administrator behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Zscaler Private Access Administrator enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler Private Access Administrator stöder **SP** och **IDP** initierad SSO

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Lägga till Zscaler Private Access Administrator från galleriet

Om du vill konfigurera integreringen av Zscaler Private Access Administrator i Azure AD måste du lägga till Zscaler Private Access Administrator från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Zscaler Private Access Administrator från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Zscaler Private Access Administrator**i sökrutan och välj **Zscaler Private Access Administrator** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

    ![Zscaler Private Access-administratör i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Zscaler Private Access Administrator baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zscaler Private Access Administrator upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Zscaler Private Access Administrator måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler Private Access Administrator Single Sign-On](#configure-zscaler-private-access-administrator-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Zscaler Private Access Administrator testanvändare](#create-zscaler-private-access-administrator-test-user)** - att ha en motsvarighet till Britta Simon i Zscaler Private Access Administrator som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Zscaler Private Access Administrator:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**på programintegreringssidan **för Zscaler Private Access Administrator** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Zscaler Private Access Administrator Domain och URL:er enkel inloggningsinformation](common/idp-relay.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `idpadminsso`

5.  Om du vill konfigurera programmet i **återupptastartat SP-läge** utför du följande steg:

    ![Zscaler Private Access Administrator Domain och URL:er enkel inloggningsinformation](common/both-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Zscaler Private Access Administrator Client support team](https://help.zscaler.com/zpa-submit-ticket) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Zscaler Private Access Administrator.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurera enkel inloggning för Zscaler Private Access Administrator

1. I ett annat webbläsarfönster loggar du till Zscaler Private Access Administrator som administratör.

2. Klicka på **Administration** högst upp och navigera till **avsnittet AUTENTISERING** på **IdP-konfiguration**.

    ![Zscaler administratör för administratör för privat åtkomst till Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicka på **Lägg till IdP-konfiguration**längst upp till höger . 

    ![Zscaler Private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Gör följande på sidan **Lägg till IdP-konfiguration:**
 
    ![Zscaler Private Access Administrator idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klicka på **Välj fil** om du vill överföra den nedladdade metadatafilen från Azure AD i fältet Ladda **upp IdP-metadatafil.**

    b. Den läser **IdP-metadata** från Azure AD och fyller i all fältinformation som visas nedan.

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Välj **Enkel inloggning** som **administratör**.

    d. Välj din domän från fältet **Domäner.**
    
    e. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler Private Access Administrator.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program**och väljer sedan **Zscaler Private Access Administrator**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Zscaler Private Access Administrator**i programlistan .

    ![Zscaler-administratören för privat åtkomst i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Skapa testanvändare för Zscaler Private Access Administrator

Om du vill att Azure AD-användare ska kunna logga in på Zscaler Private Access Administrator måste de etableras i Zscaler Private Access Administrator. När det gäller Zscaler Private Access Administrator är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på zscaler Private Access Administrator-företagswebbplatsen som administratör.

2. Klicka på **Administration** högst upp och navigera till **avsnittet AUTENTISERING** på **IdP-konfiguration**.

    ![Zscaler administratör för administratör för privat åtkomst till Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicka på **Administratörer** från vänster på menyn.

    ![Zscaler administratör för privat åtkomst](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Klicka på **Lägg till administratör**i det övre högra hörnet:

    ![Zscaler Private Access-administratör lägger till administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Gör följande på sidan **Lägg till administratör:**

    ![Zscaler Användare av Zscaler Private Access-administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. I textrutan **Användarnamn** anger du e-postmeddelandet för användaren som BrittaSimon@contoso.com.

    b. Skriv lösenordet i textrutan **Lösenord.**

    c. Skriv lösenordet i textrutan **Bekräfta lösenord.**

    d. Välj **Roll** som **Zscaler Private Access Administrator**.

    e. I textrutan **E-post** anger du e-postadressen för användaren, t.ex. BrittaSimon@contoso.com.

    f. Skriv telefonnumret i textrutan **Telefon.**

    g. Markera tidszonen i textrutan **Tidszon.**

    h. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler Private Access Administrator på åtkomstpanelen bör du automatiskt loggas in på zscalers administratör för privat åtkomst som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

