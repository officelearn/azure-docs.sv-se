---
title: 'Självstudier: Azure Active Directory-integrering med LinkedIn Learning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 618437d0007668800e0a14e8233db1676be2a364
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379271"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Självstudier: Azure Active Directory-integrering med LinkedIn Learning

I den här självstudien får lära du att integrera LinkedIn Learning med Azure Active Directory (AD Azure).

Integrera LinkedIn Learning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LinkedIn Learning
- Du kan aktivera användarna att automatiskt få loggat in på LinkedIn Learning (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LinkedIn Learning, behöver du följande objekt:

- En Azure AD-prenumeration
- En LinkedIn Learning enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till LinkedIn Learning från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-linkedin-learning-from-the-gallery"></a>Att lägga till LinkedIn Learning från galleriet
För att konfigurera integrering av LinkedIn Learning i Azure AD, som du behöver lägga till LinkedIn Learning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LinkedIn Learning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **LinkedIn Learning**. I resultatrutan, klickar du på **LinkedIn Learning** att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LinkedIn Learning baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i LinkedIn Learning är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i LinkedIn Learning upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i LinkedIn Learning.

Om du vill konfigurera och testa Azure AD enkel inloggning med LinkedIn Learning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare LinkedIn Learning](#creating-a-linkedin-learning-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LinkedIn Learning-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LinkedIn Learning:**

1. I Azure-portalen på den **LinkedIn Learning** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

1. I ett annat webbläsarfönster inloggning till din LinkedIn Learning-klient som administratör.

1. I **Kontocenter**, klickar du på **globala inställningar** under **inställningar**. Markera också **inlärning – standard** från den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

1. Klicka på **eller klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **entitets-Id** och **Assertion konsument Service (ACS)-Url**

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

1. På Azure-portalen under **LinkedIn Learning domän och URL: er**, utför följande steg om du vill konfigurera enkel inloggning i **IdP-initierad** läge

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. I den **identifierare** textrutan anger du den **entitets-ID** kopieras från LinkedIn-portalen 

    b. I den **svars-URL** textrutan anger du den **Assertion konsument Service (ACS) Url** kopieras från LinkedIn-portalen

1. Om du vill konfigurera enkel inloggning i **SP-initierat**, klicka sedan på Visa avancerade URL alternativet med inställningen i konfigurationsavsnittet anger du inloggnings-URL: en.  Att skapa din inloggnings-URL: en kopia i **Assertion konsument Service (ACS) Url** och Ersätt /saml/ med/inloggning /.   När du som har gjorts, ska inloggnings-URL: en ha följande mönster:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
1. Programmets LinkedIn Learning förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. Standardvärdet för **användaridentifierare** är **user.userprincipalname** men LinkedIn Learning förväntar sig detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller Använd rätt attribut-värde baserat på konfigurationen för din organisation. 

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/updateusermail.png)
    
1. I **användarattribut** klickar du på **visa och redigera alla andra användarattribut** och ange attribut. Användaren behöver för att lägga till fyra anspråk med namnet **e-post**, **avdelning**, **firstname**, och **lastname** och värdet är mappas med **user.mail**, **user.department**, **user.givenname**, och **user.surname** respektive

    | Attributnamn | Attributvärde |
    | --- | --- |
    | e-post| User.Mail |    
    | Avdelning| User.Department |
    | Förnamn| User.givenName |
    | Efternamn| User.surname |
    
    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. Klicka på **lägga till attributet** att öppna dialogrutan attribut.

    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**

1. Utför följande steg på den **namn** attributet -

    a. Klicka på attribut så öppnas den **redigera attributet** fönster.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/url_update.png)

    b. Ta bort URL-värdet från den **namnområde**.
    
    c. Klicka på **Ok** att spara inställningen.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

1. Klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_general_400.png)

1. Gå till **LinkedIn administratörsinställningar** avsnittet. Ladda upp XML-filen du laddade ned från Azure-portalen genom att klicka på alternativet ladda upp XML-fil.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

1. Klicka på **på** att aktivera enkel inloggning. SSO status ändras från **inte ansluten** till **ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-linkedin-learning-test-user"></a>Skapa en testanvändare LinkedIn Learning

LinkedIn Learning programmet stöder bara i tid användaretablering och -autentiserade användare skapas automatiskt i programmet. På administratören sidan Inställningar på LinkedIn Learning portal andra växeln **automatiskt tilldela licenser** till aktiva Just-in-time-etablering och detta kommer också tilldela en licens till användaren.

   ![Skapa en Azure AD-användare för testning](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LinkedIn Learning.

![Tilldela användare][200] 

**Om du vill tilldela LinkedIn Learning Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **LinkedIn Learning**.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LinkedIn Learning i åtkomstpanelen, bör du få sidan Azure inloggning och på efter lyckad inloggning, bör du få ditt LinkedIn Learning-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/linkedinlearning-tutorial/tutorial_general_203.png
