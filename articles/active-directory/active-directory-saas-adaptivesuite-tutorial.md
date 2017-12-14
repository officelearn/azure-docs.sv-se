---
title: "Självstudier: Azure Active Directory-integrering med anpassningsbar Suite | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och anpassningsbar Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 8e128ddf53a93fe30350d8e914657f3539701603
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Självstudier: Azure Active Directory-integrering med anpassningsbar Suite

I kursen får lära du att integrera anpassningsbar Suite med Azure Active Directory (AD Azure).

Integrera anpassningsbar Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till anpassningsbara Suite
- Du kan aktivera användarna att automatiskt hämta loggat in på anpassningsbar Suite (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med anpassningsbar Suite, behöver du följande:

- En Azure AD-prenumeration
- En anpassningsbar Suite enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till anpassad Suite från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adaptive-suite-from-the-gallery"></a>Att lägga till anpassad Suite från galleriet
För att konfigurera integrering av anpassningsbar Suite i Azure AD, som du behöver lägga till anpassad Suite från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till anpassad Suite från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **anpassningsbar Suite**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. Välj i resultatpanelen **anpassningsbar Suite**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med anpassningsbar Suite baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i anpassningsbar Suite motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i anpassningsbar Suite upprättas.

I anpassningsbar Suite tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med anpassningsbar Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en anpassningsbar Suite testanvändare](#creating-an-adaptive-suite-test-user)**  – du har en motsvarighet för Britta Simon i anpassningsbar Suite som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i anpassningsbar Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med anpassningsbar Suite:**

1. I Azure-portalen på den **anpassningsbar Suite** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. På den **anpassningsbar Suite domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    I den **Reply URL** textruta Skriv en URL med följande mönster:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Du får det här värdet från det anpassade paketet **SAML SSO inställningar** sidan.
    >  

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. På den **anpassningsbar Suite Configuration** klickar du på **konfigurera anpassningsbar Suite** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen anpassningsbar Suite företag som administratör.

8. Gå till **Admin**.
   
    ![Admin](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Admin")

9. I den **användare och roller** klickar du på **hantera inställningar för SAML SSO**.
   
    ![Hantera inställningar för SAML SSO](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "hantera SAML SSO-inställningar")

10. På den **SAML SSO inställningar** utför följande steg:
   
    ![Inställningar för SAML SSO](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "SAML SSO-inställningar")

    a. I den **identitet providernamn** textruta, ange ett namn för din konfiguration.
    
    b. Klistra in den **SAML enhets-ID** kopieras värdet från Azure-portalen i den **identitetsleverantör enhets-ID** textruta.
  
    c. Klistra in den **SAML inloggning tjänst-URL för enkel** kopieras värdet från Azure-portalen i den **identitetsleverantör SSO URL** textruta.
  
    d. Klistra in den **SAML enkel inloggning Tjänstwebbadress** kopieras värdet från Azure-portalen i den **anpassad logga ut URL** textruta.
  
    e. Om du vill överföra din hämtat certifikat klickar du på **Välj fil**.
  
    f. Välj följande, för:
    * **Användar-id för SAML**väljer **användarens användarnamn för anpassningsbar insikter**.
    * **Id för SAML Användarplats**väljer **användar-id i NameID ämne**.
    * **Format för SAML-NameID**väljer **e-postadress**.
    * **Aktivera SAML**väljer **Tillåt SAML SSO och anpassningsbar insikter direktinloggning**.
    
    g. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-adaptive-suite-test-user"></a>Skapa en anpassningsbar Suite testanvändare

Om du vill aktivera Azure AD-användare kan logga in på anpassningsbar Suite etableras de i anpassningsbar Suite.  

* Anpassningsbar Suite är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:** 

1. Logga in på ditt **anpassningsbar Suite** företagets webbplats som administratör.
2. Gå till **Admin**.
   
   ![Admin](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Admin")
3. I den **användare och roller** klickar du på **Lägg till användare**.
   
   ![Lägg till användare](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "lägga till användare")
4. I den **ny användare** avsnittet, utför följande steg:
   
   ![Skicka](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "skicka")   

   a. Typ av **namn**, **inloggning**, **e-post**, **lösenord** av en giltig Azure Active Directory-användare som du vill etablera i relaterade textrutor.
  
   b. Välj en **rollen**.
  
   c. Klicka på **skicka**.

>[!NOTE]
>Du kan använda något annat anpassningsbar Suite användarens konto skapas verktyg eller API: er som tillhandahålls av anpassningsbar Suite etablera AAD-användarkonton.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till anpassningsbar Suite.

![Tilldela användare][200] 

**Om du vill tilldela anpassningsbar Suite Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **anpassningsbar Suite**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Microsoft Azure AD Single Sign-On-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen anpassningsbar Suite på åtkomstpanelen du bör få automatiskt loggat in på ditt anpassningsbar Suite-program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png

