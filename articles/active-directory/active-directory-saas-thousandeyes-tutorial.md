---
title: "Självstudier: Azure Active Directory-integrering med ThousandEyes | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ThousandEyes."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: jeedes
ms.openlocfilehash: 392add7d5f0a55598b8b90760f5c3f2d1e67ac02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Självstudier: Azure Active Directory-integrering med ThousandEyes

I kursen får lära du att integrera ThousandEyes med Azure Active Directory (AD Azure).

Integrera ThousandEyes med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ThousandEyes
- Du kan aktivera användarna att automatiskt hämta loggat in på ThousandEyes (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med ThousandEyes, behöver du följande:

- En Azure AD-prenumeration
- En ThousandEyes enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ThousandEyes från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-thousandeyes-from-the-gallery"></a>Att lägga till ThousandEyes från galleriet
Du måste lägga till ThousandEyes från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ThousandEyes i Azure AD.

**Utför följande steg för att lägga till ThousandEyes från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **ThousandEyes**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. Välj i resultatpanelen **ThousandEyes**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ThousandEyes baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ThousandEyes motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ThousandEyes upprättas.

I ThousandEyes, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med ThousandEyes, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ThousandEyes](#creating-a-thousandeyes-test-user)**  – du har en motsvarighet för Britta Simon i ThousandEyes som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ThousandEyes program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ThousandEyes:**

1. I Azure-portalen på den **ThousandEyes** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. På den **ThousandEyes domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    I den **inloggnings-URL** textruta Skriv en URL som:`https://app.thousandeyes.com/login/sso`

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_400.png)

6. På den **ThousandEyes Configuration** klickar du på **konfigurera ThousandEyes** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. I en annan webbläsarfönstret, logga in på ditt **ThousandEyes** företagets webbplats som administratör.

8. Klicka på menyn högst upp **inställningar**.
   
    ![Inställningar för](./media/active-directory-saas-thousandeyes-tutorial/ic790066.png "inställningar")

9. Klicka på **konto**
   
    ![Kontot](./media/active-directory-saas-thousandeyes-tutorial/ic790067.png "konto")

10. Klicka på den **säkerhets- och** fliken.
   
    ![Säkerhets- och](./media/active-directory-saas-thousandeyes-tutorial/ic790068.png "säkerhets- och autentisering")

11. I den **installationsprogrammet för enkel inloggning** avsnittet, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-thousandeyes-tutorial/ic790069.png "Konfigurera enkel inloggning")
  
    a. Välj **aktivera enkel inloggning**.
  
    b. I **URL för inloggningssidan** textruta klistra in **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
  
    c. I **logga ut Sidadress** textruta klistra in **Sign-Out URL** som du har kopierat från Azure-portalen.
  
    d. **Identitet providern utfärdaren** textruta klistra in **SAML enhets-ID** som du har kopierat från Azure-portalen.
  
    e. I **verifieringscertifikat**, klickar du på **Välj fil**, och sedan ladda upp det certifikat som du har hämtat från Azure-portalen.
  
    f. Klicka på **Spara**.
 
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-thousandeyes-test-user"></a>Skapa en testanvändare ThousandEyes

För att aktivera Azure AD-användare att logga in på ThousandEyes etableras de i ThousandEyes.  
När det gäller ThousandEyes är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda något annat ThousandEyes användarens konto skapas verktyg eller API: er som tillhandahålls av ThousandEyes för att etablera Azure Active Directory användarkonton.

**Utför följande steg för att etablera ett användarkonto för ThousandEyes:**

1. Logga in på webbplatsen ThousandEyes företag som administratör.

2. Klicka på **inställningar**.
   
    ![Inställningar för](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "inställningar")

3. Klicka på **konto**.
   
    ![Kontot](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "konto")

4. Klicka på den **konton och användare** fliken.
   
    ![Konton och användare](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "konton och användare")

5. I den **lägga till användare och konton** avsnittet, utför följande steg:
   
    ![Lägga till användarkonton](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "lägga till användarkonton")   
  
    a. I **namn** textruta, ange namnet på användaren som **Britta Simon**.

    b. I **e-post** textruta, ange den e-posten för användare som  **brittasimon@contoso.com** .
   
    b. Klicka på **lägga till nya användare konto**.
      
     >[!NOTE]
     >Azure Active Directory kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta och aktivera kontot.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ThousandEyes.

![Tilldela användare][200] 

**Om du vill tilldela ThousandEyes Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ThousandEyes**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ThousandEyes på åtkomstpanelen du bör få automatiskt loggat in på ditt ThousandEyes program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_203.png

