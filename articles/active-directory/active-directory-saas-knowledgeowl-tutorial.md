---
title: 'Självstudier: Azure Active Directory-integrering med KnowledgeOwl | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 62dd5b4e213c3da5038e308f300685b1dad2b64d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2018
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Självstudier: Azure Active Directory-integrering med KnowledgeOwl

I kursen får lära du att integrera KnowledgeOwl med Azure Active Directory (AD Azure).

Integrera KnowledgeOwl med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till KnowledgeOwl.
- Du kan aktivera användarna att automatiskt hämta loggat in på KnowledgeOwl (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med KnowledgeOwl, behöver du följande:

- En Azure AD-prenumeration
- En KnowledgeOwl enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till KnowledgeOwl från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-knowledgeowl-from-the-gallery"></a>Att lägga till KnowledgeOwl från galleriet
Du måste lägga till KnowledgeOwl från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av KnowledgeOwl i Azure AD.

**Utför följande steg för att lägga till KnowledgeOwl från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **KnowledgeOwl**väljer **KnowledgeOwl** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![KnowledgeOwl i resultatlistan](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med KnowledgeOwl baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i KnowledgeOwl motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i KnowledgeOwl upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med KnowledgeOwl, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare KnowledgeOwl](#create-a-knowledgeowl-test-user)**  – du har en motsvarighet för Britta Simon i KnowledgeOwl som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt KnowledgeOwl program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med KnowledgeOwl:**

1. I Azure-portalen på den **KnowledgeOwl** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

3. På den **KnowledgeOwl domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och KnowledgeOwl domän med enkel inloggning information](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. I den **identifierare (enhets-ID)** textruta Skriv en URL med följande mönster:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och KnowledgeOwl domän med enkel inloggning information](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Dessa värden är inte verkliga. Du behöver uppdatera dessa värde från faktiska identifierare Reply URL och inloggnings-URL som beskrivs senare i självstudierna.

5. Programmet KnowledgeOwl förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-knowledgeowl-tutorial/attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:
    
    | Attributnamn | Attributvärde | Namnrymd|
    | ------------------- | -------------------- | -----|
    | ssoid | User.Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d.From den **Namespace** listan, ange värdet för namnrymd visas för den raden.
    
    e. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_400.png)
    
9. På den **KnowledgeOwl Configuration** klickar du på **konfigurera KnowledgeOwl** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![KnowledgeOwl konfiguration](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

10. Logga in på webbplatsen KnowledgeOwl företag som en administratör i en annan webbläsarfönster.

11. Klicka på **inställningar** och välj sedan **säkerhet**.

    ![KnowledgeOwl konfiguration](./media/active-directory-saas-knowledgeowl-tutorial/configure1.png)

12. Bläddra nedåt upp till den **SAML SSO Integration** och utför följande steg:
    
    ![KnowledgeOwl konfiguration](./media/active-directory-saas-knowledgeowl-tutorial/configure2.png)

    a. Välj **aktivera SAML SSO**.

    b. Kopiera den **SP enhets-ID** värdet och klistrar in det i den **identifierare (enhets-ID)** i den **KnowledgeOwl domän och URL: er** avsnitt på Azure-portalen.

    c. Kopiera den **SP inloggnings-URL** värdet och klistrar in det i den **inloggnings-URL och Reply URL** textrutor i den **KnowledgeOwl domän och URL: er** avsnitt på Azure-portalen.

    d. I den **IdP ID för entiteterna** textruta klistra in den **SAML enhets-ID** -värde som du har kopierat från Azure-portalen.

    e. I den **IdP inloggnings-URL** textruta klistra in den **SAML inloggning tjänst-URL för enkel** -värde som du har kopierat från Azure-portalen.

    f. I den **IdP logga ut URL** textruta klistra in den **Sign-Out URL** -värde som du har kopierat från Azure-portalen

    g. Ladda upp formuläret hämtat certifikat Azure-portalen genom att klicka på den **överför IdP certifikat**.

    h. Klicka på **kartan SAML attribut** mappa attribut och utför följande steg:
    
    ![KnowledgeOwl konfiguration](./media/active-directory-saas-knowledgeowl-tutorial/configure3.png)

    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` till den **SSO ID** textruta
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` till den **användarnamn/e-** textruta.
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` till den **Förnamn** textruta.
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` till den **efternamn** textruta.
    * Klicka på **Spara**

    i. Klicka på **Spara** längst ned på sidan.

    ![KnowledgeOwl konfiguration](./media/active-directory-saas-knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Skapa en testanvändare KnowledgeOwl

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i KnowledgeOwl. KnowledgeOwl stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt KnowledgeOwl om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [KnowledgeOwl supportteamet](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till KnowledgeOwl.

![Tilldela rollen][200] 

**Om du vill tilldela KnowledgeOwl Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **KnowledgeOwl**.

    ![Länken KnowledgeOwl i listan med program](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen KnowledgeOwl på åtkomstpanelen du bör få automatiskt loggat in på ditt KnowledgeOwl program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_203.png

