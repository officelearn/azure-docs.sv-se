---
title: "Självstudier: Azure Active Directory-integrering med mänskligheten | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och mänskligheten."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 7b9730d7d0935e5ee31437f3024588618e12b95e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Självstudier: Azure Active Directory-integrering med mänskligheten

I kursen får lära du att integrera mänskligheten med Azure Active Directory (AD Azure).

Integrera mänskligheten med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till mänskligheten
- Du kan aktivera användarna att automatiskt hämta loggat in på mänskligheten (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med mänskligheten, behöver du följande:

- En Azure AD-prenumeration
- En mänskligheten enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till mänskligheten från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-humanity-from-the-gallery"></a>Att lägga till mänskligheten från galleriet
Du måste lägga till mänskligheten från galleriet i listan över hanterade SaaS-appar för att konfigurera mänskligheten till Azure AD-integrering.

**Utför följande steg för att lägga till mänskligheten från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **mänskligheten**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_search.png)

5. Välj i resultatpanelen **mänskligheten**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med mänskligheten baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i mänskligheten motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i mänskligheten upprättas.

I mänskligheten, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med mänskligheten, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare mänskligheten](#creating-a-humanity-test-user)**  – har en motsvarighet för Britta Simon mänskligheten som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet mänskligheten.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med mänskligheten:**

1. I Azure-portalen på den **mänskligheten** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_samlbase.png)

3. På den **mänskligheten domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://company.humanity.com/includes/saml/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://company.humanity.com/app/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [mänskligheten klienten supportteamet](https://www.humanity.com/support/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_400.png)

6. På den **mänskligheten Configuration** klickar du på **konfigurera mänskligheten** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML inloggning tjänst-URL för enkel och Sign-Out URL** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_configure.png) 

7. I en annan webbläsarfönstret, logga in på ditt **mänskligheten** företagets webbplats som administratör.

8. Klicka på menyn högst upp **Admin**.
   
    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "Admin")

9. Under **integrering**, klickar du på **enkel inloggning**.
   
    ![Enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/iC786620.png "enkel inloggning")

10. I den **enkel inloggning** avsnittet, utför följande steg:
   
    ![Enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/iC786905.png "enkel inloggning")
   
    a. Välj **SAML aktiverat**.

    b. Välj **Tillåt lösenord inloggningen**.

    c. Klistra in den **SAML enkel inloggning Tjänstwebbadress** värde i den **utfärdar-URL för SAML** textruta.

    d. Klistra in den **Sign-Out URL** värde i den **Remote logga ut URL** textruta.
   
    e. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textruta.

11. Klicka på **Spara inställningar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-humanity-test-user"></a>Skapa en testanvändare mänskligheten

För att aktivera Azure AD-användare kan logga in på mänskligheten etableras de i mänskligheten. När det gäller mänskligheten är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **mänskligheten** företagets webbplats som administratör.

2. Klicka på **Admin**.
   
    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "Admin")

3. Klicka på **personal**.
   
    ![Personal](./media/active-directory-saas-shiftplanning-tutorial/ic786623.png "personal")

4. Under **åtgärder**, klickar du på **lägga till anställda**.
   
    ![Lägg till medarbetare](./media/active-directory-saas-shiftplanning-tutorial/iC786624.png "lägga till anställda")

5. I den **lägga till anställda** avsnittet, utför följande steg:
   
    ![Spara anställda](./media/active-directory-saas-shiftplanning-tutorial/iC786625.png "spara anställda")
   
    a. Typ av **Förnamn**, **efternamn**, och **e-post** av en giltig AAD-konto som du vill etablera i relaterade textrutor.

    b. Klicka på **spara anställda**.

>[!NOTE]
>Du kan använda något annat mänskligheten användarens konto skapas verktyg eller API: er som tillhandahålls av mänskligheten etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till mänskligheten.

![Tilldela användare][200] 

**Om du vill tilldela mänskligheten Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **mänskligheten**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen mänskligheten på åtkomstpanelen du bör få automatiskt loggat in på ditt mänskligheten program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_203.png

