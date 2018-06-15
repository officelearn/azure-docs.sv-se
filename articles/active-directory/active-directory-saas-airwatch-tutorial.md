---
title: 'Självstudier: Azure Active Directory-integrering med AirWatch | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 89d9bc6a40a6ee814f92cdb4c5a26e660007e64e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344552"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Självstudier: Azure Active Directory-integrering med AirWatch

I kursen får lära du att integrera AirWatch med Azure Active Directory (AD Azure).

Integrera AirWatch med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AirWatch
- Du kan aktivera användarna att automatiskt hämta loggat in på AirWatch (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med AirWatch, behöver du följande:

- En Azure AD-prenumeration
- En AirWatch enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till AirWatch från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-airwatch-from-the-gallery"></a>Att lägga till AirWatch från galleriet
Du måste lägga till AirWatch från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av AirWatch i Azure AD.

**Utför följande steg för att lägga till AirWatch från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **AirWatch**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. Välj i resultatpanelen **AirWatch**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med AirWatch baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i AirWatch motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i AirWatch upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i AirWatch.

Om du vill konfigurera och testa Azure AD enkel inloggning med AirWatch, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AirWatch](#creating-a-airwatch-test-user)**  – du har en motsvarighet för Britta Simon i AirWatch som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt AirWatch program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AirWatch:**

1. I Azure-portalen på den **AirWatch** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. På den **AirWatch domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. I den **identifierare** textruta Skriv värdet som `AirWatch`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [AirWatch klienten supportteamet](http://www.air-watch.com/company/contact-us/) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. På den **AirWatch Configuration** klickar du på **konfigurera AirWatch** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. I en annan webbläsarfönster loggar du in på webbplatsen AirWatch företag som administratör.

8. I det vänstra navigeringsfönstret klickar du på **konton**, och klicka sedan på **administratörer**.
   
   ![Administratörer](./media/active-directory-saas-airwatch-tutorial/ic791920.png "administratörer")

9. Expandera den **inställningar** -menyn och klicka sedan på **Directory Services**.
   
   ![Inställningar för](./media/active-directory-saas-airwatch-tutorial/ic791921.png "inställningar")

10. Klicka på den **användare** fliken den **Bas-DN** textruta Skriv ditt domännamn och klicka sedan på **spara**.
   
   ![Användaren](./media/active-directory-saas-airwatch-tutorial/ic791922.png "användare")

11. Klicka på den **Server** fliken.
   
   ![Server](./media/active-directory-saas-airwatch-tutorial/ic791923.png "Server")

12. Utför följande steg:
    
    ![Överför](./media/active-directory-saas-airwatch-tutorial/ic791924.png "överför")   
    
    a. Som **katalogtyp**väljer **ingen**.

    b. Välj **använder SAML för autentisering**.

    c. Ladda upp det hämta certifikatet klickar du på **överför**.

13. I den **begära** avsnittet, utför följande steg:
    
    ![Begära](./media/active-directory-saas-airwatch-tutorial/ic791925.png "begäran")  

    a. Som **begära bindning av typen**väljer **efter**.

    b. I Azure-portalen på den **Konfigurera enkel inloggning på Airwatch** dialogrutan sidan, kopiera den **SAML enkel inloggning Tjänstwebbadress** värdet och klistrar in det i den **identitet providern enkel inloggning URL: en** textruta.

    c. Som **NameID Format**väljer **e-postadress**.

    d. Klicka på **Spara**.

14. Klicka på den **användaren** fliken igen.
    
    ![Användaren](./media/active-directory-saas-airwatch-tutorial/ic791926.png "användare")

15. I den **attributet** avsnittet, utför följande steg:
    
    ![Attributet](./media/active-directory-saas-airwatch-tutorial/ic791927.png "attribut")

    a. I den **objektidentifierare** textruta typen **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. I den **användarnamn** textruta typen **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. I den **visningsnamn** textruta typen **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. I den **Förnamn** textruta typen **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. I den **efternamn** textruta typen **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. I den **e-post** textruta typen **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Klicka på **Spara**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av Britta Simon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-airwatch-test-user"></a>Skapa en testanvändare AirWatch

Om du vill aktivera Azure AD-användare kan logga in på AirWatch måste de vara etablerade i till AirWatch.

* När AirWatch, etablering är en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **AirWatch** företagets webbplats som administratör.
2. I navigeringsfönstret till vänster klickar du på **konton**, och klicka sedan på **användare**.
   
   ![Användare](./media/active-directory-saas-airwatch-tutorial/ic791929.png "användare")
3. I den **användare** -menyn klickar du på **listvyn**, och klicka sedan på **Lägg till \> Lägg till användare**.
   
   ![Lägg till användare](./media/active-directory-saas-airwatch-tutorial/ic791930.png "lägga till användare")
4. På den **Lägg till / redigera användare** dialogrutan, utför följande steg:

   ![Lägg till användare](./media/active-directory-saas-airwatch-tutorial/ic791931.png "lägga till användare")   
   1. Typ av **användarnamn**, **lösenord**, **Bekräfta lösenord**, **Förnamn**, **efternamn**,  **E-postadress** för ett giltigt Azure Active Directory-konto som du vill etablera i relaterade textrutor.
   2. Klicka på **Spara**.

>[!NOTE]
>Du kan använda något annat AirWatch användarens konto skapas verktyg eller API: er som tillhandahålls av AirWatch att etablera AAD-användarkonton.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AirWatch.

![Tilldela användare][200] 

**Om du vill tilldela AirWatch Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **AirWatch**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png

