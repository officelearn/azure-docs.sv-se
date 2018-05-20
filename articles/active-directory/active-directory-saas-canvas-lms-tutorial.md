---
title: 'Självstudier: Azure Active Directory-integrering med arbetsytan Lms | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbetsytan LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 56fe4679448fec1db67f826ff70cf8768699e507
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Självstudier: Azure Active Directory-integrering med arbetsytan LMS

I kursen får lära du att integrera arbetsytan med Azure Active Directory (AD Azure).

Integrera arbetsytan med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till arbetsytan
- Du kan aktivera användarna att automatiskt hämta loggat in på arbetsytan (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med arbetsyta, behöver du följande:

- En Azure AD-prenumeration
- En arbetsyta enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till arbetsytan från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-canvas-from-the-gallery"></a>Att lägga till arbetsytan från galleriet
Du måste lägga till arbetsytan från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av arbetsytan i Azure AD.

**Utför följande steg för att lägga till arbetsytan från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **arbetsytan**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. Välj i resultatpanelen **arbetsytan**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med arbetsytan baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i arbetsytan till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i arbetsytan upprättas.

I arbetsytan och tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med arbetsyta, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en arbetsyta testanvändare](#creating-a-canvas-test-user)**  – du har en motsvarighet för Britta Simon arbetsytan som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för arbetsytan.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med arbetsyta:**

1. I Azure-portalen på den **arbetsytan** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. På den **arbetsytan domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenant-name>.instructure.com`

    b. I den **identifierare** textruta Skriv det värde som använder följande mönster: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [arbetsytan klienten supportteamet](https://community.canvaslms.com/community/help) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_400.png)

6. På den **arbetsytan Configuration** klickar du på **konfigurera arbetsytan** att öppna **konfigurera inloggning** fönster. Kopiera den **ändra lösenord URL, Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. I en annan webbläsarfönster loggar du in på webbplatsen arbetsytan företag som administratör.

8. Gå till **kurser \> hanterade konton \> Microsoft**.
   
    ![Arbetsytan](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "arbetsytan")

9. I navigeringsfönstret till vänster, Välj **autentisering**, och klicka sedan på **Lägg till ny SAML-Config**.
   
    ![Autentisering](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "autentisering")

10. Utför följande steg på sidan aktuella integrering:
   
    ![Aktuella Integration](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "aktuella integrering")

    a. I **IdP enhets-ID** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    b. I **loggen URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. I **logga ut URL** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.

    d. I **ändra lösenord länk** textruta klistra in värdet för **ändra lösenord URL** som du har kopierat från Azure-portalen. 

    e. I **certifikat fingeravtryck** textruta klistra in den **tumavtrycket** värdet för certifikat som du har kopierat från Azure-portalen.      
        
    f. Från den **inloggningen attributet** väljer **NameID**.

    g. Från den **identifierare Format** väljer **e-postadress**.

    h. Klicka på **spara autentiseringsinställningarna**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-canvas-test-user"></a>Skapa en arbetsyta testanvändare

Om du vill aktivera Azure AD-användare kan logga in på arbetsytan, måste de etableras i arbetsytan.

Om arbetsytan är användaretablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **arbetsytan** klient.

2. Gå till **kurser \> hanterade konton \> Microsoft**.
   
   ![Arbetsytan](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "arbetsytan")

3. Klicka på **användare**.
   
   ![Användare](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "användare")

4. Klicka på **lägga till nya användare**.
   
   ![Användare](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "användare")

5. Utför följande steg på guiden Lägg till en ny användare dialogrutan sida:
   
   ![Lägg till användare](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "lägga till användare")
   
   a. I den **fullständiga namn** textruta anger du namnet på användaren som **BrittaSimon**.

   b. I den **e-post** textruta ange e-postadress för användaren som **brittasimon@contoso.com**.

   c. I den **inloggning** textruta ange användarens Azure AD e-postadress som **brittasimon@contoso.com**.

   d. Välj **e-användaren om det här kontot skapas**.

   e. Klicka på **lägga till användare**.

>[!NOTE]
>Du kan använda något annat arbetsytan användarens konto skapas verktyg eller API: er som tillhandahålls av arbetsytan etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till arbetsytan.

![Tilldela användare][200] 

**Om du vill tilldela arbetsytan Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **arbetsytan**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på arbetsytan panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt program i arbetsytan.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_203.png

