---
title: 'Självstudier: Azure Active Directory-integrering med ScaleX Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64edf2aa47211c1d2a598417a7b2edc00f260075
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208579"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Självstudier: Azure Active Directory-integrering med ScaleX Enterprise

Lär dig hur du integrerar ScaleX Enterprise med Azure Active Directory (AD Azure) i den här självstudien.

Integrera ScaleX Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ScaleX Enterprise
- Du kan aktivera användarna att automatiskt få loggat in på ScaleX Enterprise (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i. Vad är programåtkomst och enkel inloggning med [Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ScaleX Enterprise, behöver du följande objekt:

- En Azure AD-prenumeration
- En ScaleX Enterprise enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ScaleX Enterprise från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Att lägga till ScaleX Enterprise från galleriet
Om du vill konfigurera integreringen av ScaleX Enterprise i Azure AD, som du behöver lägga till ScaleX Enterprise från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ScaleX Enterprise från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **ScaleX Enterprise**.

    ![Skapa en Azure AD-användare för testning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

1. I resultatpanelen väljer **ScaleX Enterprise**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ScaleX Enterprise baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i ScaleX Enterprise till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ScaleX företag upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i ScaleX företag.

Om du vill konfigurera och testa Azure AD enkel inloggning med ScaleX Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i ScaleX företag som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ScaleX Enterprise-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ScaleX Enterprise:**

1. I Azure-portalen på den **ScaleX Enterprise** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

1. På den **ScaleX Enterprise domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. I den **identifierare** textrutan skriver du värdet med följande mönster: `https://platform.rescale.com/saml2/<company id>/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Kontrollera **visa avancerade URL-inställningar**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    I den **inloggnings-URL** textrutan skriver du värdet med följande mönster: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Det här är inte de verkliga värdena. Uppdatera dessa värden med faktiska identifierare, svars-URL eller inloggnings-URL. Kontakta [ScaleX Enterprise Client supportteamet](https://info.rescale.com/contact_sales) att hämta dessa värden. 

1. Programmets ScaleX förväntar sig SAML-intyg i ett visst format, som kräver att du vill ändra anpassade attributmappningar i SAML-tokenattribut konfigurationen. Klicka på **visa och redigera alla andra användarattribut** kryssrutan för att öppna anpassat attribut inställningar.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Högerklicka på attributet **namn** och klicka på Ta bort.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Klicka på **e-postadress** attribut för att öppna fönstret Redigera attributet. Ändra värdet från **user.mail** till **user.userprincipalname** och klicka på Ok.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
1. På den **ScaleX företagskonfiguration** klickar du på **konfigurera ScaleX Enterprise** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** och **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

1. Att konfigurera enkel inloggning på **ScaleX Enterprise** sida, inloggning till webbplatsen för företagets ScaleX Enterprise som administratör.

1. Klicka på menyn längst upp till höger och sedan **Contoso Administration**.

    > [!NOTE] 
    > Contoso är bara ett exempel. Det bör vara faktiska företagets namn. 

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/Test_Admin.png) 

1. Välj **integreringar** från den översta menyn och välj **enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/admin_sso.png) 

1. Fyll i formuläret på följande sätt:

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Välj **”skapa alla användare som kan autentisera med enkel inloggning”.**

    b. **Tjänstleverantör saml**: Klistra in värdet ***urn: oasis: namn: tc: SAML:2.0:nameid-format: permanent***

    c. **Namn på identitetsprovider e-fält i ACS-svaret**: Klistra in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identitets-Provider EntityDescriptor entitets-ID:** Klistra in den **SAML entitets-ID** värdet som har kopierats från Azure-portalen.

    e. **SingleSignOnService URL för identitetsprovider:** Klistra in den **SAML enkel inloggning för tjänst-URL** från Azure-portalen.

    f. **Providern offentliga X509 identitetscertifikat:** Öppna X509 certifikatet hämtas från Azure i anteckningar och klistra in innehållet i den här rutan. Se till att det finns inga radbrytningar i mitten av certifikat-innehållet.
    
    g. Markera kryssrutorna för följande: **Aktiverat, kryptera NameID och logga AuthnRequests.**

    h. Klicka på **SSO uppdateringsinställningar** att spara inställningarna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Skapa en testanvändare ScaleX Enterprise

Om du vill aktivera Azure AD-användare att logga in på ScaleX Enterprise, måste de vara etablerade i Enterprise-ScaleX. När det gäller ScaleX Enterprise, etablering är en automatisk uppgift och inga manuella steg krävs. Alla användare som kan autentisera med autentiseringsuppgifter för enkel inloggning ska etableras automatiskt på ScaleX sida.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja användaråtkomst till ScaleX Enterprise.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon ScaleX Enterprise, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **ScaleX Enterprise**.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

Klicka på panelen ScaleX Enterprise i åtkomstpanelen, du kommer få automatiskt loggat in på dina ScaleX företagsprogram. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

