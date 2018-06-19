---
title: 'Självstudier: Azure Active Directory-integrering med LinkedIn Learning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8c507e7b411d0200ff5f8a7b12ab904d5ab63e90
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35962160"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Självstudier: Azure Active Directory-integrering med LinkedIn Learning

I kursen får lära du att integrera LinkedIn Learning med Azure Active Directory (AD Azure).

Integrera LinkedIn Learning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LinkedIn-utbildning
- Du kan aktivera användarna att automatiskt hämta loggat in på LinkedIn Learning (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LinkedIn Learning behöver du följande:

- En Azure AD-prenumeration
- En LinkedIn Learning enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till LinkedIn Learning från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-linkedin-learning-from-the-gallery"></a>Lägga till LinkedIn Learning från galleriet
Du måste lägga till LinkedIn Learning från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av LinkedIn Learning i Azure AD.

**Utför följande steg för att lägga till LinkedIn Learning från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **LinkedIn Learning**. I resultatrutan, klickar du på **LinkedIn Learning** lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med LinkedIn Learning baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i LinkedIn Learning motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LinkedIn Learning upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i LinkedIn utbildning.

Om du vill konfigurera och testa Azure AD enkel inloggning med LinkedIn Learning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LinkedIn Learning](#creating-a-linkedin-learning-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LinkedIn Learning-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LinkedIn Learning:**

1. I Azure-portalen på den **LinkedIn Learning** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. I en annan webbläsarfönster inloggning till LinkedIn Learning-klient som administratör.

4. I **Kontocenter**, klickar du på **globala inställningar** under **inställningar**. Markera också **utbildning - standard** från den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Klicka på **eller klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **enhets-Id** och **Assertion konsumenten Access (ACS) Url**

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. På Azure-portalen under **LinkedIn Learning domän och URL: er**, utför följande steg om du vill konfigurera enkel inloggning i **IdP initierade** läge

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. I den **identifierare** textruta, ange den **enhets-ID** kopieras från LinkedIn-portalen 

    b. I den **Reply URL** textruta ange den **Assertion konsumenten Access (ACS) Url** kopieras från LinkedIn-portalen

7. Om du vill konfigurera enkel inloggning i **SP-initierad**, klickar du på Visa avancerade URL inställningen alternativ i konfigurationsavsnittet och konfigurera URL inloggning med följande mönster:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. Tillämpningsprogrammet LinkedIn Learning förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här. Standardvärdet för **användar-ID** är **user.userprincipalname** men LinkedIn Learning förväntar detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller använda rätt attribut-värde baserat på konfigurationen för din organisation. 

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/updateusermail.png)
    
9. I **användarattribut** klickar du på **visa och redigera andra användarattribut** och ange attribut. Användaren behöver lägga till fyra anspråk med namnet **e-post**, **avdelning**, **Förnamn**, och **efternamn** och värdet är för att mappa med **user.mail**, **user.department**, **user.givenname**, och **user.surname** respektive

    | Attributnamn | Attributvärde |
    | --- | --- |
    | e-post| User.Mail |    
    | avdelning| User.Department |
    | Förnamn| User.givenName |
    | Efternamn| User.surname |
    
    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. Klicka på **lägga till attributet** att öppna dialogrutan attribut.

    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

10. Utför följande steg på den **namn** attribut -

    a. Klicka på attributet för att öppna den **Redigera attribut** fönster.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/url_update.png)

    b. Ta bort URL-värdet från den **namnområde**.
    
    c. Klicka på **Ok** spara inställningen.

11. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

12. Klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_general_400.png)

13. Gå till **LinkedIn admininställningar** avsnitt. Överför den XML-fil som du hämtade från Azure portal genom att klicka på alternativet överför XML-fil.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. Klicka på **på** att aktivera enkel inloggning. SSO status ändras från **inte ansluten** till **ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-linkedin-learning-test-user"></a>Skapa en testanvändare LinkedIn-utbildning

LinkedIn Learning programmet stöder bara i tid användaretablering och authentication-användare skapas automatiskt i programmet. Om administratören sidan Inställningar på LinkedIn Learning portal Vänd växeln **automatiskt tilldela licenser** till aktiva enbart i tid etablering och detta kan också tilldela en licens till användaren. LinkedIn Learning stöder även automatisk användaretablering, kan du hitta mer information [här](linkedinlearning-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

   ![Skapa en testanvändare i Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LinkedIn Learning.

![Tilldela användare][200] 

**Om du vill tilldela LinkedIn Learning Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **LinkedIn Learning**.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen LinkedIn Learning på åtkomstpanelen du bör få sidan Azure inloggning och på efter lyckad inloggning kan du bör få till LinkedIn Learning programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](linkedinlearning-provisioning-tutorial.md)

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