---
title: 'Självstudier: Azure Active Directory-integrering med T & E Express | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och T & E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 8d3980b6169c8a9fc60c04e527814159082a46e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350227"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Självstudier: Azure Active Directory-integrering med T & E Express

I kursen får lära du att integrera T & E Express med Azure Active Directory (AD Azure).

Integrera T & E Express med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till d & E Express
- Du kan aktivera användarna att automatiskt hämta inloggade & E Express (Single Sign-On) till med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med T & E Express, behöver du följande:

- En Azure AD-prenumeration
- En T & E Express enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till d & E Express från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-te-express-from-the-gallery"></a>Att lägga till d & E Express från galleriet
Du måste lägga till d & E Express från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av d & E Express i Azure AD.

**Utför följande steg för att lägga till d & E Express från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **T & E Express**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_search.png)

5. Välj i resultatpanelen **T & E Express**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med T & E Express baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i T & E Express motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i T & E Express upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i T & E Express.

Om du vill konfigurera och testa Azure AD enkel inloggning med T & E Express, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare T & E Express](#creating-a-te-express-test-user)**  – du har en motsvarighet för Britta Simon i T & E Express som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i T & E Express-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med T & E Express:**

1. I Azure-hanteringsportalen på den **T & E Express** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

3. På den **& E Express domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. I den **identifierare** textruta Skriv värdet som: `https://<domain>.tyeexpress.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med de faktiska identifierare och Reply-URL. Här rekommenderar vi att du om du vill använda det unika värdet på strängen i identifieraren. Kontakta [T & E Express supportteam](http://www.tyeexpress.com/contacto.aspx) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_400.png)

8. Konfigurera enkel inloggning på **T & E snabb** sida, inloggning till T & E express program utan SAML för enkel inloggning på med administratörsautentiseringsuppgifter.

9. Under den **Admin** klickar du på **SAML domän** att öppna inställningssidan SAML.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tyeexpress-tutorial/tye-SAML.png)

10. Välj den **Activar(Activate)** alternativet från **nr** till **SI(Yes)**. I den **identitet providern Metadata** textruta klistra in metadata XML som du har donwloaded från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tyeexpress-tutorial/tyeAdmin.png)

11. Klicka på den **Guardar(Save)** för att spara inställningarna. 


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-te-express-test-user"></a>Skapa en testanvändare T & E Express

För att aktivera Azure AD-användare loggar in i T & E Express, måste de etableras i T & E Express.  
Vid T & E Express är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på webbplatsen T & E Express företag som administratör.

2. Klicka på användare för att öppna sidan som användare under Admin-taggen.

    ![Lägga till medarbetare](./media/active-directory-saas-tyeexpress-tutorial/tye-adminusers.png)

3. På sidan, klickar du på **+** att lägga till användarna.

    ![Lägga till medarbetare](./media/active-directory-saas-tyeexpress-tutorial/tye-usershome.png)

4. Ange alla obligatoriska uppgifter som frågan i formuläret och klicka på Spara för att spara informationen.

    ![Lägga till medarbetare](./media/active-directory-saas-tyeexpress-tutorial/tye-usersadd.png)

    ![Lägga till medarbetare](./media/active-directory-saas-tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till d & E Express.

![Tilldela användare][200] 

**Utför följande steg om du vill tilldela Britta Simon & E Express till:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **T & E Express**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen T & E Express på åtkomstpanelen du bör få automatiskt loggat in på ditt d & E Express-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_203.png

