---
title: 'Självstudier: Azure Active Directory-integrering med ON24 virtuell miljö SAML anslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ON24 virtuell miljö SAML anslutning.
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
ms.openlocfilehash: a0b5dd169d29dc392274ab5589931f37beb04e9b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273608"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Självstudier: Azure Active Directory-integrering med ON24 virtuell miljö SAML anslutning

I den här självstudien får du lära dig hur du integrerar ON24 virtuell miljö SAML anslutning med Azure Active Directory (AD Azure).
Integrera ON24 virtuell miljö SAML anslutning med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ON24 virtuell miljö SAML anslutning.
* Du kan aktivera användarna att vara automatiskt inloggad till ON24 virtuell miljö SAML-anslutning (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med ON24 virtuell miljö SAML anslutning behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ON24 virtuell miljö SAML anslutning enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för ON24 virtuell miljö SAML anslutning **SP** och **IDP** -initierad SSO

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Att lägga till ON24 virtuell miljö SAML-anslutning från galleriet

Om du vill konfigurera integreringen av ON24 virtuell miljö SAML anslutning till Azure AD, som du behöver lägga till ON24 virtuell miljö SAML anslutning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ON24 virtuell miljö SAML anslutning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **ON24 virtuell miljö SAML anslutning**väljer **ON24 virtuell miljö SAML anslutning** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![ON24 virtuell miljö SAML-anslutningen i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ON24 virtuell miljö SAML anslutning baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ON24 virtuell miljö SAML anslutning ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ON24 virtuell miljö SAML-anslutning, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ON24 virtuell miljö SAML anslutning enkel inloggning](#configure-on24-virtual-environment-saml-connection-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ON24 virtuell miljö SAML anslutning testanvändare](#create-on24-virtual-environment-saml-connection-test-user)**  – du har en motsvarighet för Britta Simon i ON24 virtuell miljö SAML anslutning som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med ON24 virtuell miljö SAML-anslutning:

1. I den [Azure-portalen](https://portal.azure.com/)på den **ON24 virtuell miljö SAML anslutning** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![ON24 virtuell miljö SAML anslutning domän och URL: er med enkel inloggning för information](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du en URL:

     **URL: en för produktion-miljö**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL för QA-miljön**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. I textrutan **Svars-URL** skriver du en URL:

     **URL: en för produktion-miljö**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL för QA-miljön**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Klicka på **Ange ytterligare URL:er**. 

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Om du vill konfigurera programmet i **SP** initierade läge, utföra följande steg:

    ![ON24 virtuell miljö SAML anslutning domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska Vidarebefordransstatus och inloggnings-URL: en. Kontakta [ON24 virtuell miljö SAML anslutning klienten supportteamet](https://www.on24.com/contact-us/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera ON24 virtuell miljö SAML anslutning** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Konfigurera ON24 virtuell miljö SAML anslutning enkel inloggning

Att konfigurera enkel inloggning på **ON24 virtuell miljö SAML anslutning** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopieras URL: er från Azure portal för att [ ON24 virtuell miljö SAML anslutning supportteamet](https://www.on24.com/about-us/support/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ON24 virtuell miljö SAML anslutning.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **ON24 virtuell miljö SAML anslutning**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **ON24 virtuell miljö SAML anslutning**.

    ![Länken ON24 virtuell miljö SAML anslutning i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Skapa ON24 virtuell miljö SAML anslutning testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i ON24 virtuell miljö SAML anslutning. Arbeta med [ON24 virtuell miljö SAML anslutning supportteamet](https://www.on24.com/about-us/support/) att lägga till användare i ON24 virtuell miljö SAML anslutning-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ON24 virtuell miljö SAML anslutning i åtkomstpanelen, bör det vara loggas in automatiskt till ON24 virtuell miljö SAML anslutningen som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

