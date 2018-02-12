---
title: "Självstudier: Azure Active Directory-integrering med arbetsplats av Facebook | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbetsplats med Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 524319deea957809440436b3cac2b0256516923a
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Självstudier: Azure Active Directory-integrering med arbetsplats av Facebook

I kursen får lära du att integrera arbetsplats av Facebook med Azure Active Directory (AD Azure).

Integrera arbetsplats av Facebook med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till arbetsplats med Facebook
- Du kan aktivera användarna att automatiskt hämta loggat in på arbetsplatsen av Facebook (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med arbetsplats av Facebook, behöver du följande:

- En Azure AD-prenumeration
- En arbetsplats av Facebook enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till arbetsplatsen av Facebook från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Att lägga till arbetsplatsen av Facebook från galleriet
Du måste lägga till arbetsplatsen av Facebook från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av arbetsplatsen av Facebook i Azure AD.

**Utför följande steg för att lägga till arbetsplatsen av Facebook från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **arbetsplats av Facebook**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. Välj i resultatpanelen **arbetsplats av Facebook**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning till arbetsplats av Facebook baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i arbetsplats av Facebook till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i arbetsplats av Facebook upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** arbetsplatsen av Facebook.

Om du vill konfigurera och testa Azure AD enkel inloggning till arbetsplats av Facebook, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Konfigurera omautentisering frekvens](#configuring-reauthentication-frequency)**  - om du vill konfigurera arbetsplats för att fråga efter en SAML-kontroll.
3. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en arbetsplats av Facebook testanvändare](#creating-a-workplace-by-facebook-test-user)**  – du har en motsvarighet för Britta Simon arbetsplatsen av Facebook som är kopplad till Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet kan du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning på din arbetsplats genom Facebook-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning till arbetsplats av Facebook:**

1. I Azure-portalen på den **arbetsplats av Facebook** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. På den **arbetsplats Facebook-domänen och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<instancename>.facebook.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Visas på sidan autentisering på arbetsplatsen företagets instrumentpanelen för korrekta värden för din arbetsplats. 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_400.png)

6. På den **arbetsplats Facebook konfigurationen** klickar du på **konfigurera arbetsplats av Facebook** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workplacebyfacebook-tutorial/config.png) 

7. I en annan webbläsarfönstret, inloggning till arbetsplatsen av Facebook företagets plats som administratör.
  
   > [!NOTE] 
   > Som en del av processen för SAML-autentisering, kan arbetsplatsen använda frågesträngar upp till 2,5 kilobyte i storlek för att kunna skicka parametrar till Azure AD.

8. I den **företagets instrumentpanelen**, gå till den **autentisering** fliken.

9. Under **SAML-autentisering**väljer **SSO endast** från den nedrullningsbara listan.

10. Ange värden som kopieras från **arbetsplats Facebook konfigurationen** på Azure portal till motsvarande fält:

    *   I **SAML URL** textruta klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
    *   I **utfärdar-URL för SAML-textrutan**, klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.
    *   I **SAML logga ut omdirigera** (valfritt), klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.
    *   Öppna din **Base64-kodat certifikat** i anteckningar som hämtas från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **SAML certifikat** textruta.

11. Du kan behöva målgruppen webbadressen till mottagaren URL och ACS (Assertion konsumenten Service)-URL som visas under den **SAML-konfiguration** avsnitt.

12. Bläddra längst ned i avsnittet och klicka på den **Test SSO** knappen. Detta resulterar i ett popup-fönster som visas med Azure AD-inloggningssidan visas. Ange dina autentiseringsuppgifter i som vanligt för att autentisera. 

    **Felsöka:** kontrollera e-postadressen som returneras tillbaka från Azure AD är samma som arbetsplatskontot som du är inloggad med.

13. När testet har slutförts, bläddra till längst ned på sidan och klicka på den **spara** knappen.

14. Alla användare som använder arbetsplats visas nu med Azure AD-inloggningssidan för autentisering.

15. **SAML logga ut omdirigera (valfritt)** - 

    Kan du också konfigurera en SAML logga ut Url som kan användas för att peka på sidan för Azure AD logga ut. När den här inställningen är aktiverad och konfigurerad, kommer användaren inte längre dirigeras till sidan Arbetsyta logga ut. I stället omdirigeras användaren till den url som har lagts till i inställningen för att dirigera om SAML logga ut.


> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="configuring-reauthentication-frequency"></a>Konfigurera omautentisering frekvens

Du kan konfigurera arbetsplats för att fråga efter en SAML-kontroll varje dag, tre dagar, vecka, vecka, månad eller aldrig.

> [!NOTE] 
>Minsta värde för SAML-kontroll av mobila program anges till en vecka.

Du kan också tvinga en återställning för alla användare med hjälp av knappen SAML: kräver SAML-autentisering för alla användare nu.


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Skapa en arbetsplats av Facebook testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i arbetsplats med Facebook. Arbetsplats av Facebook stöder just-in-time-allokering som är aktiverad som standard.

Det finns inga åtgärder i det här avsnittet. Om en användare inte finns i arbetsplats av Facebook, skapas en ny när du försöker komma åt arbetsplatsen av Facebook.

>[!Note]
>Om du behöver skapa en användare manuellt, kontakta [arbetsplats av Facebook klienten supportteamet](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till arbetsplats med Facebook.

![Tilldela användare][200] 

**Om du vill tilldela arbetsplats av Facebook Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **arbetsplats av Facebook**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](active-directory-saas-workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_203.png

