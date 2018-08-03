---
title: 'Självstudier: Azure Active Directory-integration med SAML SSO för antal samverkande resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO för antal samverkande resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: c1a1126026f3d2618a0669e4bd69a84cc1c6c54c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431631"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Självstudier: Azure Active Directory-integration med SAML SSO för antal samverkande resolution GmbH

I den här självstudien får du lära dig hur du integrerar SAML SSO för antal samverkande resolution GmbH med Azure Active Directory (AD Azure).

Integrera SAML SSO för antal samverkande resolution GmbH med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAML SSO för antal samverkande resolution GmbH
- Du kan aktivera användarna att automatiskt få loggat in på SAML SSO för antal samverkande resolution GmbH (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAML SSO för antal samverkande resolution GmbH, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAML SSO för växer samman av upplösning GmbH, enkel inloggning på aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAML SSO för växer samman med upplösning GmbH från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Att lägga till SAML SSO för växer samman med upplösning GmbH från galleriet

För att konfigurera integrering av SAML SSO för antal samverkande resolution GmbH i Azure AD, som du behöver lägga till SAML SSO för antal samverkande resolution GmbH från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till SAML SSO för antal samverkande resolution GmbH från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **SAML SSO för antal samverkande resolution GmbH**.

    ![Skapa en Azure AD-användare för testning](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_search.png)

1. I resultatpanelen väljer **SAML SSO för antal samverkande resolution GmbH**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAML SSO för antal samverkande resolution GmbH baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i SAML SSO för antal samverkande resolution GmbH är att en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i SAML SSO för antal samverkande resolution GmbH måste upprättas.

I SAML SSO för antal samverkande resolution GmbH, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAML SSO för antal samverkande resolution GmbH, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en SAML SSO för växer samman av lösning GmbH testanvändare](#creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user)**  – du har en motsvarighet för Britta Simon i SAML SSO för antal samverkande resolution GmbH som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAML SSO för antal samverkande resolution GmbH program.

**Om du vill konfigurera Azure AD enkel inloggning med SAML SSO för antal samverkande resolution GmbH, utför du följande steg:**

1. I Azure-portalen på den **SAML SSO för antal samverkande resolution GmbH** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_samlbase.png)

1. På den **SAML SSO växer samman resolution GmbH domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_1.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

1. Kontrollera **visa avancerade URL-inställningar**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_2.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [SAML SSO för antal samverkande resolution GmbH klienten supportteam](https://www.resolution.de/go/support) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/tutorial_general_400.png)    
    
1. Logga in på i ett annat webbläsarfönster din **SAML SSO för växer samman av lösning GmbH administrationsportalen** som administratör.

1. Hovra över kugghjulet och klicka på den **tillägg**.
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon1.png)

1. Du omdirigeras till sidan för administratörsåtkomst. Ange lösenordet och klicka på **Bekräfta** knappen.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon2.png)

1. Under **ATLASSIANS MARKETPLACE** fliken **hitta nya tillägg**. 

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon.png)

1. Sök **SAML enkel inloggning (SSO) för antal samverkande** och klicka på **installera** knappen för att installera den nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon7.png)

1. Installationen av plugin-programmet startas. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon8.png)

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon9.png)

1.  Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon10.png)
    
1. Klicka på **konfigurera** att konfigurera nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon11.png)

1. Den här nya plugin-program även finns under **användare och säkerhet** fliken.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon3.png)
    
1. På **SAML SingleSignOn plugin-programmet Configuration** klickar du på **lägga till nya IDP: N** knappen för att konfigurera inställningarna för identitetsprovidern.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon4.png)

1. På **väljer SAML-identitetsprovider** utför följande steg:

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Ange **Azure AD** som typ av IDP: N.
    
    b. Lägg till **namn** identitetsleverantörens (t.ex Azure AD).
    
    c. Lägg till **beskrivning** identitetsleverantörens (t.ex Azure AD).
    
    d. Klicka på **Nästa**.
    
1. På **identitet providerkonfigurationen** klickar du på **nästa** knappen.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5b.png)

1. På **importera SAML IdP Metadata** utför följande steg:

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Klicka på **Läs in fil** knappen och välj Metadata-XML-fil som du hämtade i steg 5.

    b. Klicka på **Import** knappen.
    
    c. Vänta en kort stund tills importen lyckas.
    
    d. Klicka på **nästa** knappen.
    
1. På **användar-ID-attribut och omvandling** klickar du på **nästa** knappen.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon5d.png)
    
1. På **skapa användare och uppdatera** klickar du på **Spara & nästa** att spara inställningarna.    
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon6a.png)
    
1. På **testa dina inställningar** klickar du på **hoppa över testet och konfigurera manuellt** att hoppa över användare testet just nu. Detta kommer att utföras i nästa avsnitt och kräver en del inställningar i Azure-portalen. 
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon6b.png)
    
1. I dialogrutan-läsning apprearing **hoppar över testning innebär...** , klickar du på **OK**.
    
    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/addon6c.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/samlssoconfluence-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/samlssoconfluence-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/samlssoconfluence-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/samlssoconfluence-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Skapa en SAML SSO för växer samman av lösning GmbH testanvändare

Om du vill aktivera Azure AD-användare att logga in på SAML SSO för antal samverkande resolution GmbH, måste de etablerats i SAML SSO för växer samman av lösning GmbH.  
I SAML SSO för antal samverkande resolution GmbH är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din SAML SSO för växer samman av lösning GmbH företagets plats som administratör.

1. Hovra över kugghjulet och klicka på den **Användarhantering**.

    ![Lägg till medarbetare](./media/samlssoconfluence-tutorial/user1.png) 

1. Under avsnittet för användare, klickar du på **lägga till användare** fliken. På den **”Lägg till en användare”** dialogrutan utför följande steg:

    ![Lägg till medarbetare](./media/samlssoconfluence-tutorial/user2.png) 

    a. I den **användarnamn** textrutan Skriv e-postadress för användaren som Britta Simon.

    b. I den **fullständigt namn** textrutan skriver du det fullständiga namnet på användaren som Britta Simon.

    c. I den **e-post** textrutan typ e-postadressen för användaren som Brittasimon@contoso.com.

    d. I den **lösenord** textrutan skriver du lösenordet för Britta Simon.

    e. Klicka på **Bekräfta lösenord** ange lösenordet igen.
    
    f. Klicka på **Lägg till** knappen.    

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAML SSO för antal samverkande resolution GmbH.

![Tilldela användare][200] 

**Om du vill tilldela SAML SSO för antal samverkande Britta Simon resolution GmbH, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **SAML SSO för antal samverkande resolution GmbH**.

    ![Konfigurera enkel inloggning](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på SAML SSO för växer samman av lösning GmbH panel i åtkomstpanelen du bör få automatiskt loggat in på ditt SAML SSO för antal samverkande resolution GmbH program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samlssoconfluence-tutorial/tutorial_general_01.png
[2]: ./media/samlssoconfluence-tutorial/tutorial_general_02.png
[3]: ./media/samlssoconfluence-tutorial/tutorial_general_03.png
[4]: ./media/samlssoconfluence-tutorial/tutorial_general_04.png

[100]: ./media/samlssoconfluence-tutorial/tutorial_general_100.png

[200]: ./media/samlssoconfluence-tutorial/tutorial_general_200.png
[201]: ./media/samlssoconfluence-tutorial/tutorial_general_201.png
[202]: ./media/samlssoconfluence-tutorial/tutorial_general_202.png
[203]: ./media/samlssoconfluence-tutorial/tutorial_general_203.png

