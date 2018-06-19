---
title: 'Självstudier: Azure Active Directory-integrering med Workfront | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workfront.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: ffc226527fb52b992c7c639522ccf4085ee32146
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963917"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Självstudier: Azure Active Directory-integrering med Workfront

I kursen får lära du att integrera Workfront med Azure Active Directory (AD Azure).

Integrera Workfront med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Workfront
- Du kan aktivera användarna att automatiskt hämta loggat in på Workfront (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Workfront, behöver du följande:

- En Azure AD-prenumeration
- En Workfront enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Workfront från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workfront-from-the-gallery"></a>Att lägga till Workfront från galleriet
Du måste lägga till Workfront från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Workfront i Azure AD.

**Utför följande steg för att lägga till Workfront från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Workfront**.

    ![Skapa en testanvändare i Azure AD](./media/workfront-tutorial/tutorial_workfront_search.png)

5. Välj i resultatpanelen **Workfront**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/workfront-tutorial/tutorial_workfront_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Workfront baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Workfront motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Workfront upprättas.

I Workfront, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workfront, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Workfront](#creating-a-workfront-test-user)**  – du har en motsvarighet för Britta Simon i Workfront som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Workfront program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Workfront:**

1. I Azure-portalen på den **Workfront** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/workfront-tutorial/tutorial_workfront_samlbase.png)

3. På den **Workfront domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/workfront-tutorial/tutorial_workfront_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.attask-ondemand.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Workfront klienten supportteamet](https://www.workfront.com/contact-us/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/workfront-tutorial/tutorial_workfront_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/workfront-tutorial/tutorial_general_400.png)

6. På den **Workfront Configuration** klickar du på **konfigurera Workfront** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/workfront-tutorial/tutorial_workfront_configure.png) 

7. Inloggning på webbplatsen Workfront företag som administratör.

8. Gå till **enkel inloggning på konfigurationen**.

9. På den **enkel inloggning** dialogrutan, utför följande steg
    
    ![Konfigurera enkel inloggning][23]
   
    a. Som **typen**väljer **SAML 2.0**.
   
    b. Välj **Service Provider-ID**.
   
    c. Klistra in den **SAML inloggning tjänst-URL för enkel** till den **Portal Inloggningswebbadressen** textruta.
   
    d. Klistra in **tjänst-URL för enkel Sign-Out** till den **Sign-Out URL** textruta.
   
    e. Klistra in **ändra lösenord URL** till den **ändra lösenord URL** textruta.
   
    f. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/workfront-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/workfront-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/workfront-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/workfront-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-workfront-test-user"></a>Skapa en testanvändare Workfront

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Workfront.

**Utför följande steg för att skapa en användare som kallas Britta Simon i Workfront:**

1. Logga in på webbplatsen Workfront företag som administratör.
2. Klicka på menyn högst upp **personer**.
3. Klicka på **ny Person**. 
4. I dialogrutan Ny Person att utföra följande steg:
   
    ![Skapa en testanvändare Workfront][21] 
   
    a. I den **Förnamn** textruta skriver ”Britta”.
   
    b. I den **efternamn** textruta skriver ”Simon”.
   
    c. I den **e-postadress** textruta Skriv Britta Simon e-postadress i Azure Active Directory.
   
    d. Klicka på **lägga till personen**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workfront.

![Tilldela användare][200] 

**Om du vill tilldela Workfront Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Workfront**.

    ![Konfigurera enkel inloggning](./media/workfront-tutorial/tutorial_workfront_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Du bör få inloggningssidan för Workfront program när du klickar på panelen Workfront på åtkomstpanelen.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/workfront-tutorial/tutorial_general_01.png
[2]: ./media/workfront-tutorial/tutorial_general_02.png
[3]: ./media/workfront-tutorial/tutorial_general_03.png
[4]: ./media/workfront-tutorial/tutorial_general_04.png
[21]:./media/workfront-tutorial/tutorial_attask_08.png
[23]:./media/workfront-tutorial/tutorial_attask_06.png
[100]: ./media/workfront-tutorial/tutorial_general_100.png

[200]: ./media/workfront-tutorial/tutorial_general_200.png
[201]: ./media/workfront-tutorial/tutorial_general_201.png
[202]: ./media/workfront-tutorial/tutorial_general_202.png
[203]: ./media/workfront-tutorial/tutorial_general_203.png

