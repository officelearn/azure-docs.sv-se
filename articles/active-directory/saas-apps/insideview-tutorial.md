---
title: 'Självstudier: Azure Active Directory-integrering med InsideView | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 8bfb98dd3b97a306aae33c450cb19429fa135109
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219925"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Självstudier: Azure Active Directory-integrering med InsideView

I kursen får lära du att integrera InsideView med Azure Active Directory (AD Azure).

Integrera InsideView med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till InsideView
- Du kan aktivera användarna att automatiskt hämta loggat in på InsideView (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med InsideView, behöver du följande:

- En Azure AD-prenumeration
- En InsideView enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till InsideView från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-insideview-from-the-gallery"></a>Att lägga till InsideView från galleriet
Du måste lägga till InsideView från galleriet i listan över hanterade SaaS-appar för att konfigurera InsideView i till Azure AD-integrering.

**Utför följande steg för att lägga till InsideView från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **InsideView**.

    ![Skapa en testanvändare i Azure AD](./media/insideview-tutorial/tutorial_insideview_search.png)

5. Välj i resultatpanelen **InsideView**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med InsideView baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i InsideView motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i InsideView upprättas.

I InsideView, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med InsideView, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare InsideView](#creating-a-insideview-test-user)**  – du har en motsvarighet för Britta Simon i InsideView som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt InsideView program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med InsideView:**

1. I Azure-portalen på den **InsideView** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_samlbase.png)

3. På den **InsideView domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_url.png)
    
    I den **Reply URL** textruta Skriv en URL med följande mönster: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska Reply-URL. Kontakta [InsideView supportteamet ](mailto:support@insideview.com) att hämta det här värdet.
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_general_400.png)

6. På den **InsideView Configuration** klickar du på **konfigurera InsideView** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen InsideView företag som administratör.

8. Klicka på i verktygsfältet högst upp **Admin**, **SingleSignOn inställningar**, och klicka sedan på **lägga till SAML**.
   
   ![SAML för enkel inloggning inställningar](./media/insideview-tutorial/ic794135.png "SAML för enkel inloggning inställningar")

9. I den **lägga till en ny SAML** avsnittet, utför följande steg:

    ![Lägg till en ny SAML](./media/insideview-tutorial/ic794136.png "lägga till en ny SAML")
   
    a. I den **STS Name** textruta, ange ett namn för din konfiguration.

    b. I **SamlP/WS-Fed oönskade EndPoint** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
    
    c. Öppna Base64-kodade certifikatet, som du har hämtat från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **STS Certificate** textruta.

    d. I den **Crm användar-Id-mappning** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
        
    e. I den **Crm e-mappning** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. I den **Crm första mappning** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    g. I den **Crm efternamn mappning** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/insideview-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/insideview-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/insideview-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/insideview-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-insideview-test-user"></a>Skapa en testanvändare InsideView

Om du vill aktivera Azure AD-användare kan logga in på InsideView måste de vara etablerade i till InsideView. När det gäller InsideView är etablering en manuell aktivitet.

För att få användare eller kontakter som skapats i InsideView, kontakta [InsideView supportteamet](mailto:support@insideview.com).

>[!NOTE]
>Du kan använda något annat InsideView användarens konto skapas verktyg eller API: er som tillhandahålls av InsideView att etablera Azure AD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till InsideView.

![Tilldela användare][200] 

**Om du vill tilldela InsideView Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **InsideView**.

    ![Konfigurera enkel inloggning](./media/insideview-tutorial/tutorial_insideview_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen InsideView på åtkomstpanelen du bör få automatiskt loggat in på ditt InsideView program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insideview-tutorial/tutorial_general_01.png
[2]: ./media/insideview-tutorial/tutorial_general_02.png
[3]: ./media/insideview-tutorial/tutorial_general_03.png
[4]: ./media/insideview-tutorial/tutorial_general_04.png

[100]: ./media/insideview-tutorial/tutorial_general_100.png

[200]: ./media/insideview-tutorial/tutorial_general_200.png
[201]: ./media/insideview-tutorial/tutorial_general_201.png
[202]: ./media/insideview-tutorial/tutorial_general_202.png
[203]: ./media/insideview-tutorial/tutorial_general_203.png

