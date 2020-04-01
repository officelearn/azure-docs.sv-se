---
title: 'Självstudiekurs: Azure Active Directory-integrering med OrgChart nu | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095433"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Självstudiekurs: Azure Active Directory-integrering med OrgChart nu

I den här självstudien får du lära dig hur du integrerar OrgChart Now med Azure Active Directory (Azure AD).
Genom att integrera OrgChart Now med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till OrgChart Now.
* Du kan aktivera dina användare så att de automatiskt loggas in på OrgChart Now (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med OrgChart Now behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* OrgChart Nu enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* OrgChart Nu stöder **SP** och **IDP** initierade SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Lägga till OrgChart Nu från galleriet

Om du vill konfigurera integreringen av OrgChart Now i Azure AD måste du lägga till OrgChart Now från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till OrgChart Now från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **OrgChart Now**i sökrutan och välj **OrgChart Now** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![OrgChart Nu i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med OrgChart Now baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i OrgChart Now upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med OrgChart Now måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera OrgChart Now Single Sign-On](#configure-orgchart-now-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa OrgChart Now testanvändare](#create-orgchart-now-test-user)** - att ha en motsvarighet till Britta Simon i OrgChart Nu som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med OrgChart Now:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning på**sidan Programintegrering av **OrgChart Now** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![OrgChart Now Domän och WEBBADRESSER enkel inloggningsinformation](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://sso2.orgchartnow.com`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>`är **Azure AD-identifieraren** som kopierats från avsnittet **Konfigurera OrgChart Now,** som beskrivs senare i självstudiekursen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera organisationsschema nu.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-orgchart-now-single-sign-on"></a>Konfigurera orgchart nu enkel inloggning

Om du vill konfigurera enkel inloggning på sidan **OrgChart Now** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för OrgChart Now](mailto:ocnsupport@officeworksoftware.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till OrgChart Now.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **OrgChart Now**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **OrgChart Now**i programlistan .

    ![Länken OrgChart Now i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-orgchart-now-test-user"></a>Skapa testanvändare för OrgChart Nu

Om du vill att Azure AD-användare ska kunna logga in på OrgChart Now måste de etableras i OrgChart Now. 

1. OrgChart Nu stöder just-in-time-etablering, vilket är aktiverat som standard. En ny användare skapas under ett försök att komma åt OrgChart Nu om det inte finns ännu. Just-in-time-användarens etableringsfunktionen skapar bara en **skrivskyddad** användare när en SSO-begäran kommer från en erkänd IDP och e-postmeddelandet i SAML-påståendet inte finns i användarlistan. För den här funktionen för automatisk etablering måste du skapa en åtkomstgrupp med namnet **Allmänt** i Organisationsschema nu. Följ stegen nedan för att skapa en åtkomstgrupp:

    a. Gå till alternativet **Hantera grupper** när du har klickat på **växeln** i det övre högra hörnet av användargränssnittet.

    ![Grupper i OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Markera ikonen **Lägg till** och namnge gruppen **Allmänt** och klicka sedan på **OK**. 

    ![OrgChart lägg nu till](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Välj den eller de mappar som du vill att de allmänna eller skrivskyddade användarna ska kunna komma åt:

    ![Mappar i OrgChart Nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Lås** mapparna så att endast administratörsanvändare kan ändra dem. Tryck sedan på **OK**.

    ![OrgChart nu lås](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Om du vill skapa **administratörsanvändare** och **läsa/skriva** användare måste du manuellt skapa en användare för att få tillgång till deras behörighetsnivå via SSO. Gör följande för att etablera ett användarkonto:

    a. Logga in på OrgChart Now som säkerhetsadministratör.

    b.  Klicka på **Inställningar** längst upp till höger och navigera sedan till **Hantera användare**.

    ![Inställningar för OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klicka på **Lägg till** och utför följande steg:

    ![OrgChart hantera nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * I textrutan **Användar-ID** anger du användar-ID:t som **\@brittasimon contoso.com**.

    * I textrutan **E-postadress** anger du e-post för användare som **\@brittasimon contoso.com**.

    * Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen OrgChart Now på åtkomstpanelen ska du automatiskt loggas in på den Organisationsuppsättning nu som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

