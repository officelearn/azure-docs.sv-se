---
title: 'Självstudier: Azure Active Directory-integrering med LinkedIn försäljning Navigator | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedInSalesNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 15d8e14c0df4ddbf4526cda62af97a1f5b7feb01
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966615"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Självstudier: Azure Active Directory-integrering med LinkedIn försäljning Navigator

Lär dig hur du integrerar Navigatören för LinkedIn försäljning med Azure Active Directory (AD Azure) i den här självstudiekursen.

Integrera LinkedIn försäljning Navigator med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LinkedIn försäljning Navigator
- Du kan aktivera användarna att automatiskt hämta loggat in på LinkedIn försäljning Navigator (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD Bläddra [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LinkedIn försäljning Navigator behöver du följande:

- En Azure AD-prenumeration
- En LinkedIn försäljning Navigator enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Undvik att använda i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till LinkedIn försäljning Navigator från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Lägga till LinkedIn försäljning Navigator från galleriet
Du måste lägga till LinkedIn försäljning Navigator från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av LinkedIn försäljning Navigator i Azure AD.

**Utför följande steg för att lägga till LinkedIn försäljning Navigator från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **LinkedIn försäljning Navigator**.

    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

5. Välj i resultatpanelen **LinkedIn försäljning Navigator**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med LinkedIn försäljning Navigator baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i LinkedIn försäljning Navigator motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i LinkedIn försäljning Navigator upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i LinkedIn försäljning Navigator.

Om du vill konfigurera och testa Azure AD enkel inloggning med LinkedIn försäljning Navigator, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LinkedIn försäljning Navigator](#creating-a-linkedin-sales-navigator-test-user)**  – du har en motsvarighet för Britta Simon i LinkedIn försäljning Navigator som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet LinkedIn försäljning Navigator.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LinkedIn försäljning Navigator:**

1. I Azure-portalen på den **LinkedIn försäljning Navigator** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan i **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

3. I en annan webbläsarfönster inloggning till din **LinkedIn försäljning Navigator** webbplats som administratör.

4. I **Kontocenter**, klickar du på **globala inställningar** under **inställningar**. Markera också **försäljning Navigator** från den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

5. Klicka på **eller klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **enhets-Id** och **Assertion konsumenten Access (ACS) Url**.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

6. På Azure-portalen under **URL: er och domänen för LinkedIn förs Navigator** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. I den **identifierare** textruta, ange den **enhets-ID** kopieras från LinkedIn-portalen 

    b. I den **Reply URL** textruta ange den **Assertion konsumenten Access (ACS) Url** kopieras från LinkedIn-portalen

7. Kontrollera **visa avancerade inställningar för URL: en**, om du vill konfigurera programmet i **SP** initierade läge.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

8. Din **LinkedIn försäljning Navigator** program förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel. Standardvärdet för **användar-ID** är **user.userprincipalname** men LinkedIn försäljning Navigator förväntar att det ska mappas med användarens e-postadress. Du kan använda **user.mail** attribut i listan eller använda rätt attribut-värde baserat på konfigurationen för din organisation. 

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
9. I **användarattribut** klickar du på **visa och redigera andra användarattribut** och ange attribut. Användaren behöver lägga till fyra anspråk med namnet **e-post**, **avdelning**, **Förnamn**, och **efternamn** och värdet är för att mappa med **user.mail**, **user.department**, **user.givenname**, och **user.surname** respektive

    | Attributnamn | Attributvärde |
    | --- | --- |    
    | e-post| User.Mail |
    | avdelning| User.Department |
    | Förnamn| User.givenName |
    | Efternamn| User.surname |
    
    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Klicka på **lägga till attributet** att öppna dialogrutan attribut.
    
    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

10. Utför följande steg på den **namn** attribut -

    a. Klicka på attributet för att öppna den **Redigera attribut** fönster.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. Ta bort URL-värdet från den **namnområde**.
    
    c. Klicka på **Ok** spara inställningen.

11. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

12. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

13. Gå till **LinkedIn admininställningar** avsnitt. Klicka på **överför XML-filen** att överföra Metadata XML-filen som du har hämtat från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

14. Klicka på **på** att aktivera enkel inloggning. SSO status ändras från **inte ansluten** till **ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Skapa en testanvändare LinkedIn försäljning Navigator

Länkade försäljning Navigator programmet stöder bara i tid JIT-användaretablering och authentication-användare skapas automatiskt i programmet. Aktivera **automatiskt tilldela licenser** tilldela en licens till användaren.
   
   ![Skapa en testanvändare i Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LinkedIn försäljning Navigator.

![Tilldela användare][200] 

**Om du vill tilldela LinkedIn försäljning Navigator Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **LinkedIn försäljning Navigator**.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen LinkedIn försäljning Navigator på åtkomstpanelen bör du omdirigeras till organisationens sida där du måste ange ditt personliga LinkedIn-kontoinformation. Den länkar ditt eget konto med ditt företag LinkedIn-konto. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

