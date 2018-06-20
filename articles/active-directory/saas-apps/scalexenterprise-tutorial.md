---
title: 'Självstudier: Azure Active Directory-integrering med ScaleX Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 502531ef14858316df8fc7d92fab7ce9160efb20
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210429"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Självstudier: Azure Active Directory-integrering med ScaleX Enterprise

I kursen får lära du att integrera ScaleX Enterprise med Azure Active Directory (AD Azure).

Integrera ScaleX Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ScaleX Enterprise
- Du kan aktivera användarna att automatiskt hämta loggat in på ScaleX Enterprise (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns i. Vad är programåtkomst och enkel inloggning med [Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ScaleX Enterprise, behöver du följande:

- En Azure AD-prenumeration
- En ScaleX Enterprise enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om detta är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ScaleX Enterprise från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Att lägga till ScaleX Enterprise från galleriet
Du måste lägga till ScaleX Enterprise från galleriet i listan över hanterade SaaS-appar för att konfigurera ScaleX företag i Azure AD-integrering.

**Utför följande steg för att lägga till ScaleX Enterprise från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **ScaleX Enterprise**.

    ![Skapa en testanvändare i Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. Välj i resultatpanelen **ScaleX Enterprise**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ScaleX Enterprise baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ScaleX Enterprise motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ScaleX Enterprise upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i ScaleX företag.

Om du vill konfigurera och testa Azure AD enkel inloggning med ScaleX Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i ScaleX företag som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ScaleX Enterprise-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ScaleX Enterprise:**

1. I Azure-portalen på den **ScaleX Enterprise** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. På den **ScaleX företagsdomänen och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. I den **identifierare** textruta Skriv det värde som använder följande mönster: `https://platform.rescale.com/saml2/<company id>/`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://platform.rescale.com/saml2/<company id>/acs/`

4. Kontrollera **visa avancerade inställningar för URL: en**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Detta är inte de verkliga värden. Uppdatera dessa värden med den faktiska identifierare, Reply URL eller inloggnings-URL. Kontakta [ScaleX Enterprise-klienten supportteamet](http://info.rescale.com/contact_sales) att hämta dessa värden. 

5. Tillämpningsprogrammet ScaleX förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan ändra det anpassade attributmappningar i konfigurationen för SAML-token attribut. Klicka på **visa och redigera andra användarattribut** kryssrutan för att öppna ett anpassat attribut inställningar.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Högerklicka på attributet **namnet** och klicka på Ta bort.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Klicka på **e-postadress** attribut för att öppna fönstret Redigera attribut. Ändra dess värde från **user.mail** till **user.userprincipalname** och klicka på Ok.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
7. På den **ScaleX företagskonfiguration** klickar du på **konfigurera ScaleX Enterprise** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** och **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Konfigurera enkel inloggning på **ScaleX Enterprise** sida, inloggning till ScaleX Enterprise webbplatsen som en administratör.

9. Klicka på menyn i övre högra och välj **Contoso Administration**.

    > [!NOTE] 
    > Contoso är bara ett exempel. Detta bör vara faktiska företagets namn. 

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/Test_Admin.png) 

10. Välj **integreringar** översta menyn och välj **enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/admin_sso.png) 

11. Fyll i formuläret på följande sätt:

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Välj **”skapa alla användare som kan autentisera med enkel inloggning”.**

    b. **Saml-leverantör**: klistra in värdet ***urn: oasis: namn: tc: SAML:2.0:nameid-format: beständiga***

    c. **Namnet på identitetsleverantör e-fält i ACS-svar**: klistra in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identitet providern EntityDescriptor enhets-ID:** klistra in den **SAML enhets-ID** kopieras värdet från Azure-portalen.

    e. **Identitet providern SingleSignOnService URL:** klistra in den **SAML inloggning tjänst-URL för enkel** från Azure-portalen.

    f. **Providern offentliga X509 identitetscertifikat:** öppna X509 certifikat hämtas från Azure i anteckningar och klistra in innehållet i den här rutan. Se till att det finns inga radbrytningar i mitten av certifikat-innehållet.
    
    g. Markera kryssrutorna för följande: **aktiverad, kryptera NameID och logga AuthnRequests.**

    h. Klicka på **SSO uppdateringsinställningar** spara inställningarna.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Skapa en testanvändare ScaleX Enterprise

Om du vill aktivera Azure AD-användare kan logga in på ScaleX Enterprise, måste de vara etablerade i till ScaleX företaget. Etablering är en automatisk uppgift vid ScaleX Enterprise, och inga manuella steg krävs. Alla användare som kan autentisera med autentiseringsuppgifter för enkel inloggning ska etableras automatiskt på ScaleX sida.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja användaråtkomst till ScaleX Enterprise.

![Tilldela användare][200] 

**Om du vill tilldela ScaleX Enterprise Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ScaleX Enterprise**.

    ![Konfigurera enkel inloggning](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Klicka på panelen ScaleX Enterprise på åtkomstpanelen, du ska hämta automatiskt loggat in på ditt ScaleX företagsprogram. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

