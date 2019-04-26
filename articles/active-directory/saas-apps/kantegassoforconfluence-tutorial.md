---
title: 'Självstudier: Azure Active Directory-integrering med Kantega SSO för antal samverkande | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för växer samman.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 210d69256f2e7f4727ee866af71dd72e765fb0b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60265733"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Självstudier: Azure Active Directory-integrering med Kantega SSO för antal samverkande

I den här självstudien får du lära dig hur du integrerar Kantega SSO för växer samman med Azure Active Directory (AD Azure).

Integrera Kantega SSO för växer samman med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Kantega SSO för antal samverkande
- Du kan aktivera användarna att automatiskt få loggat in på Kantega SSO för antal samverkande (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Kantega SSO för antal samverkande, behöver du följande objekt:

- En Azure AD-prenumeration
- En Kantega SSO för antal samverkande enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Kantega SSO för antal samverkande från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Att lägga till Kantega SSO för antal samverkande från galleriet
Om du vill konfigurera integreringen av Kantega SSO för växer samman i Azure AD, som du behöver lägga till Kantega SSO för antal samverkande från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kantega SSO för antal samverkande från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Kantega SSO för antal samverkande**.

    ![Skapa en Azure AD-användare för testning](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_search.png)

1. I resultatpanelen väljer **Kantega SSO för antal samverkande**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Kantega SSO för antal samverkande baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Kantega SSO för antal samverkande är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Kantega SSO för antal samverkande upprättas.

I Kantega SSO för antal samverkande, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kantega SSO för antal samverkande, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Kantega SSO för antal samverkande testanvändare](#creating-a-kantega-sso-for-confluence-test-user)**  – du har en motsvarighet för Britta Simon i Kantega SSO för antal samverkande som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Kantega SSO för växer samman program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Kantega SSO för antal samverkande:**

1. I Azure-portalen på den **Kantega SSO för antal samverkande** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_samlbase.png)

1. I **IDP** initiera läge för den **Kantega SSO växer samman domän och URL: er** avsnittet utföra följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url1.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. I **SP** initierad läge, kontrollera **visa avancerade URL-inställningar** och utföra följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url2.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av Confluence-pluginprogrammet, som beskrivs senare i självstudien.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/tutorial_general_400.png)
    
1. Logga in på i ett annat webbläsarfönster din **växer samman administrationsportalen** som administratör.

1. Hovra över kugghjulet och klicka på **Tillägg**.
    
    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Klicka på **Hitta nya tillägg** under fliken **ATLASSIAN MARKETPLACE**. 

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon.png)

1. Sök **Kantega SSO för antal samverkande SAML Kerberos** och klicka på **installera** knappen för att installera den nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Installationen av plugin-programmet startar.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon3.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon33.png)

1.  Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon34.png)
    
1. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Det nya plugin-programmet visas även under fliken **Användare och säkerhet**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon36.png)
    
1. I den **SAML** avsnittet. Välj **Azure Active Directory (Azure AD)** från den **Lägg till identitetsprovider** listrutan.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Välj prenumerationsnivå som **grundläggande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon5.png)     

1. På den **appegenskaper** avsnittet, gör du följande: 

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Kopiera den **Appidentitets-URI** värde och använda det som **identifierare, svars-URL och inloggnings-URL** på den **Kantega SSO växer samman domän och URL: er** avsnitt i Azure-portalen.

    b. Klicka på **Nästa**.

1. På den **Metadata import** avsnittet, gör du följande: 

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Välj **Metadata-filen på datorn**, och ladda upp metadatafilen, som du har hämtat från Azure-portalen.

    b. Klicka på **Nästa**.

1. På den **namn och SSO plats** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon8.png)
    
    a. Lägg till namnet på identitetsprovider i **namn på identitetsprovider** textrutan (t.ex Azure AD).

    b. Klicka på **Nästa**.

1. Verifiera certifikat för signering och klicka på **nästa**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon9.png)

1. På den **växer samman användarkonton** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Välj **skapa användare i antal Samverkandes intern katalog om det behövs** och ange rätt namn i gruppen för användare (kan vara flera Nej. av (grupper avgränsade med kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Slutför**.    

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon11.png)

1. På den **kända domäner för Azure AD** avsnittet, gör du följande: 

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Välj **kända domäner** från den vänstra panelen på sidan.

    b. Ange domännamnet i den **kända domäner** textrutan.

    c. Klicka på **Spara**. 

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/kantegassoforconfluence-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/kantegassoforconfluence-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/kantegassoforconfluence-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/kantegassoforconfluence-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-kantega-sso-for-confluence-test-user"></a>Skapa en Kantega SSO för antal samverkande testanvändare

Om du vill aktivera Azure AD-användare att logga in på växer samman etableras de i växer samman. När det gäller Kantega SSO för antal samverkande är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din Kantega SSO för antal samverkande företagets webbplats som administratör.

1. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/kantegassoforconfluence-tutorial/user1.png) 

1. Under avsnittet för användare, klickar du på **Lägg till användare** fliken. Utför följande steg i dialogrutan **Lägg till en användare**:

    ![Lägga till medarbetare](./media/kantegassoforconfluence-tutorial/user2.png) 

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. I textrutan **Fullständigt namn** skriver du det fullständiga namnet för användaren: Britta Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    d. I den **lösenord** textrutan skriver du lösenordet för användaren.

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.
    
    f. Klicka på knappen **Lägg till**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kantega SSO för växer samman.

![Tilldela användare][200] 

**Om du vill tilldela Kantega SSO för antal samverkande Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Kantega SSO för antal samverkande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Kantega SSO för antal samverkande panel i åtkomstpanelen du bör få automatiskt loggat in på ditt Kantega SSO för växer samman program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforconfluence-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforconfluence-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforconfluence-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforconfluence-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforconfluence-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforconfluence-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforconfluence-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforconfluence-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforconfluence-tutorial/tutorial_general_203.png

