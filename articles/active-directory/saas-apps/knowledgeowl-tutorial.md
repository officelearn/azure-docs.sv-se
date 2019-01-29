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
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 84f6b42e932838413aafd7fa142067e1c72584ed
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168744"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Självstudier: Azure Active Directory-integrering med KnowledgeOwl

I den här självstudien får du lära dig hur du integrerar KnowledgeOwl med Azure Active Directory (AD Azure).

Integrera KnowledgeOwl med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till KnowledgeOwl.
- Du kan aktivera användarna att automatiskt få loggat in på KnowledgeOwl (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med KnowledgeOwl, behöver du följande objekt:

- En Azure AD-prenumeration
- En KnowledgeOwl enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till KnowledgeOwl från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-knowledgeowl-from-the-gallery"></a>Att lägga till KnowledgeOwl från galleriet
För att konfigurera integrering av KnowledgeOwl i Azure AD, som du behöver lägga till KnowledgeOwl från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till KnowledgeOwl från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **KnowledgeOwl**väljer **KnowledgeOwl** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![KnowledgeOwl i resultatlistan](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med KnowledgeOwl baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i KnowledgeOwl är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i KnowledgeOwl upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med KnowledgeOwl, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare KnowledgeOwl](#create-a-knowledgeowl-test-user)**  – du har en motsvarighet för Britta Simon i KnowledgeOwl som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt KnowledgeOwl program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med KnowledgeOwl:**

1. I Azure-portalen på den **KnowledgeOwl** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. På den **KnowledgeOwl domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![KnowledgeOwl domän och URL: er med enkel inloggning för information](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. I textrutan **Svars-URL** anger du en URL med följande mönster:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![KnowledgeOwl domän och URL: er med enkel inloggning för information](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
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
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värdet från faktiska identifierare svars-URL och inloggnings-URL som beskrivs senare i självstudien.

1. KnowledgeOwl programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program.

    ![Konfigurera enkel inloggning](./media/knowledgeowl-tutorial/attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
    
    | Attributnamn | Attributvärde | Namnområde|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d.From den **Namespace** listan, anger du namnområdet-värde som visas för den raden.
    
    e. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. På den **KnowledgeOwl Configuration** klickar du på **konfigurera KnowledgeOwl** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. Logga in på webbplatsen KnowledgeOwl företag som en administratör i ett annat webbläsarfönster.

1. Klicka på **inställningar** och välj sedan **Security**.

    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure1.png)

1. Bläddra till **SAML SSO-integrering** och utför följande steg:
    
    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure2.png)

    a. Välj **aktivera SAML SSO**.

    b. Kopiera den **SP entitets-ID** värde och klistra in den i den **identifierare (entitets-ID)** i den **KnowledgeOwl domän och URL: er** avsnittet på Azure portal.

    c. Kopiera den **SP inloggnings-URL** värde och klistra in den i den **inloggnings-URL och svars-URL** textrutor i den **KnowledgeOwl domän och URL: er** avsnittet på Azure portal.

    d. I den **IdP entityID** textrutan klistra in den **SAML entitets-ID** värde, som du har kopierat från Azure-portalen.

    e. I den **inloggnings-URL för IDP: N** textrutan klistra in den **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen.

    f. I den **utloggnings-URL för IDP: N** textrutan klistra in den **URL: en för utloggning** värde, som du har kopierat från Azure portal

    g. Ladda upp nedladdade certifikatet från Azure portal genom att klicka på den **överför certifikat för IDP: N**.

    h. Klicka på **kartan SAML-attribut** att mappa attribut och utför följande steg:
    
    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure3.png)

    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` till den **SSO-ID** textrutan
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` till den **användarnamn/e-post** textrutan.
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` till den **Förnamn** textrutan.
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` till den **efternamn** textrutan.
    * Klicka på **Spara**

    i. Klicka på **Spara** längst ned på sidan.

    ![KnowledgeOwl konfiguration](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Skapa en KnowledgeOwl testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i KnowledgeOwl. KnowledgeOwl stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt KnowledgeOwl om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [KnowledgeOwl supportteamet](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till KnowledgeOwl.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon KnowledgeOwl, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **KnowledgeOwl**.

    ![Länken KnowledgeOwl i listan med program](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen KnowledgeOwl i åtkomstpanelen du bör få automatiskt loggat in på ditt KnowledgeOwl program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

