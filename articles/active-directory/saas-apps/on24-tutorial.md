---
title: 'Självstudiekurs: Azure Active Directory-integrering med ON24-SAML-anslutning för virtuell miljö | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 801a631b56a11e68c444ede846ff82195cd7627f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095727"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Självstudiekurs: Azure Active Directory-integrering med ON24 SAML-anslutning för virtuell miljö

I den här självstudien får du lära dig hur du integrerar ON24 Virtual Environment SAML Connection med Azure Active Directory (Azure AD).
Genom att integrera SAML-anslutning för virtuell miljö på ON24 med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ON24 Virtual Environment SAML-anslutning.
* Du kan aktivera dina användare så att de automatiskt loggas in på ON24 Virtual Environment SAML Connection (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med ON24-SAML-anslutning för virtuell miljö behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ON24 SAML-anslutning för virtuell miljö med enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ON24 SAML-anslutning för virtuell miljö stöder **SP** och **IDP** initierad SSO

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Lägga till ON24 Virtual Environment SAML-anslutning från galleriet

Om du vill konfigurera integreringen av ON24 Virtual Environment SAML Connection i Azure AD måste du lägga till ON24 Virtual Environment SAML-anslutning från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SAML-anslutning för virtuell miljö i galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ON24 VIRTUAL Environment SAML Connection**, välj **ON24 Virtual Environment SAML Connection** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

     ![ON24 SAML-anslutning för virtuell miljö i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med ON24 Virtual Environment SAML Connection baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ON24 Virtual Environment SAML-anslutning upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med ON24 VIRTUAL Environment SAML-anslutning måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ON24 Virtual Environment SAML Connection Single Sign-On](#configure-on24-virtual-environment-saml-connection-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ON24 Virtual Environment SAML Connection testanvändare](#create-on24-virtual-environment-saml-connection-test-user)** - att ha en motsvarighet till Britta Simon i ON24 Virtual Environment SAML-anslutning som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en azure AD-inloggning med ON24-SAML-anslutning för virtuell miljö:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**på sidan FÖR SAML-anslutningsapplikation för **ON24 Virtual Environment.**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![ON24 SAML-anslutningsdomän för virtuell miljö och url:er med enkel inloggning](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du en URL: 

     **URL för produktionsmiljö**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL för QA-miljö**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Skriv en URL i textrutan **Svars-URL**: 

     **URL för produktionsmiljö**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL för QA-miljö**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Klicka på **Ange ytterligare URL:er**. 

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Om du vill konfigurera programmet i **återupptastartat SP-läge** utför du följande steg:

    ![ON24 SAML-anslutningsdomän för virtuell miljö och url:er med enkel inloggning](common/both-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska relay-state- och inloggnings-URL:en. Kontakta [ON24 Supportteamet för virtual environment SAML-anslutningsklient](https://www.on24.com/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera ON24-anslutning** till virtuell miljö.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Konfigurera EN SAML-anslutning för virtuell miljö

Om du vill konfigurera enstaka inloggning på **SAML-anslutningssidan** för virtuell miljö på ON24 måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [ON24 SAML-anslutningssupportteamet](https://www.on24.com/about-us/support/)för virtuell miljö . De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ON24 Virtual Environment SAML Connection.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **ON24 SAML-anslutning för virtuell miljö**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **ON24 SAML-anslutning**för virtuell miljö i programlistan .

    ![DEN ON24 Verklig Miljö SAML Connection länk inne om Ansökan ta upp på en lista](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Skapa EN ON24 SAML-anslutningstestanvändare för virtuell miljö

I det här avsnittet skapar du en användare som heter Britta Simon i ON24 Virtual Environment SAML Connection. Arbeta med [ON24 Virtual Environment SAML Connection supportteam](https://www.on24.com/about-us/support/) för att lägga till användare i ON24 Virtual Environment SAML Connection plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ON24 Virtual Environment SAML Connection på åtkomstpanelen bör du automatiskt loggas in på den SAML-anslutning för virtuell miljö som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

