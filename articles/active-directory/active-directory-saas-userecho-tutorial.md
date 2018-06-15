---
title: 'Självstudier: Azure Active Directory-integrering med UserEcho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 29a02a5324344330dae3f2e47a09c94343e7355e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350968"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Självstudier: Azure Active Directory-integrering med UserEcho

I kursen får lära du att integrera UserEcho med Azure Active Directory (AD Azure).

Integrera UserEcho med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till UserEcho
- Du kan aktivera användarna att automatiskt hämta loggat in på UserEcho (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med UserEcho, behöver du följande:

- En Azure AD-prenumeration
- En UserEcho enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till UserEcho från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-userecho-from-the-gallery"></a>Att lägga till UserEcho från galleriet
Du måste lägga till UserEcho från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av UserEcho i Azure AD.

**Utför följande steg för att lägga till UserEcho från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **UserEcho**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_search.png)

5. Välj i resultatpanelen **UserEcho**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med UserEcho baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i UserEcho motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i UserEcho upprättas.

I UserEcho, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med UserEcho, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare UserEcho](#creating-a-userecho-test-user)**  – du har en motsvarighet för Britta Simon i UserEcho som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt UserEcho program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med UserEcho:**

1. I Azure-portalen på den **UserEcho** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_samlbase.png)

3. På den **UserEcho domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.userecho.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [UserEcho klienten supportteamet](https://feedback.userecho.com/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_general_400.png)

6. På den **UserEcho Configuration** klickar du på **konfigurera UserEcho** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_configure.png) 

7. I ett nytt webbläsarfönster inloggning till webbplatsen UserEcho företag som administratör.

8. I verktygsfältet högst upp på ditt användarnamn så utökas menyn och klicka sedan på **installationsprogrammet**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

9. Klicka på **integreringar**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 

10. Klicka på **webbplats**, och klicka sedan på **enkel inloggning (SAML2)**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 

11. På den **enkel inloggning (SAML)** utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)
    
    a. Som **SAML-aktiverade**väljer **Ja**.
    
    b. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure-portalen i den **SAML SSO URL** textruta.
    
    c. Klistra in **Sign-Out URL**, som du har kopierat från Azure-portalen i den **Remote logoout URL** textruta.
    
    d. Öppna din hämtat certifikat i anteckningar, kopiera innehållet och klistrar in det i den **X.509-certifikat** textruta.
    
    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-userecho-test-user"></a>Skapa en testanvändare UserEcho

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i UserEcho.

**Utför följande steg för att skapa en användare som kallas Britta Simon i UserEcho:**

1. Inloggning på webbplatsen UserEcho företag som administratör.

2. I verktygsfältet högst upp på ditt användarnamn så utökas menyn och klicka sedan på **installationsprogrammet**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

3. Klicka på **användare**, för att expandera den **användare** avsnitt.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

4. Klicka på **användare**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

5. Klicka på **bjuda in en ny användare**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)

6. På den **bjuda in en ny användare** dialogrutan, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a. I den **namn** textruta, ange namnet på användaren som Britta Simon.
    
    b.  I den **e-post** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.
    
    c. Klicka på **bjuda in**.

En inbjudan har skickats till Britta, vilket gör att hon vill börja använda UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till UserEcho.

![Tilldela användare][200] 

**Om du vill tilldela UserEcho Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **UserEcho**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen UserEcho på åtkomstpanelen du bör få automatiskt loggat in på ditt UserEcho program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png

