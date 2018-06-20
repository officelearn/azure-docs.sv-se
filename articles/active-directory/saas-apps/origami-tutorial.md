---
title: 'Självstudier: Azure Active Directory-integrering med Origami | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 019d05cfb0339341e541e6fb3327568e8639bcda
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230142"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Självstudier: Azure Active Directory-integrering med Origami

I kursen får lära du att integrera Origami med Azure Active Directory (AD Azure).

Integrera Origami med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Origami
- Du kan aktivera användarna att automatiskt hämta loggat in på Origami (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Origami behöver du följande:

- En Azure AD-prenumeration
- En Origami enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Origami från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-origami-from-the-gallery"></a>Att lägga till Origami från galleriet
Du måste lägga till Origami från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Origami i Azure AD.

**Utför följande steg för att lägga till Origami från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Origami**.

    ![Skapa en testanvändare i Azure AD](./media/origami-tutorial/tutorial_origami_search.png)

5. Välj i resultatpanelen **Origami**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Origami baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Origami motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Origami upprättas.

I Origami, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Origami, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Origami](#creating-an-origami-test-user)**  – har en motsvarighet för Britta Simon Origami som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Origami.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Origami:**

1. I Azure-portalen på den **Origami** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_samlbase.png)

3. På den **Origami domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta [Origami klienten supportteamet](https://wordpress.org/support/theme/origami) värdet hämtas. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_general_400.png)

6. På den **Origami Configuration** klickar du på **konfigurera Origami** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_configure.png) 

7. Logga in på Origami-konto med administratörsrättigheter.

8. Klicka på menyn högst upp **Admin**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_51.png)

9. Utför följande steg på enkel inloggning på sidan Inställningar av dialogrutan:
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_531.png)

    a. Välj **aktivera enkel inloggning på**.

    b. I den **identitetsleverantören logga in Sidadress** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    c. I den **identitetsleverantörens Sign-out Sidadress** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    d. Klicka på **Bläddra** att ladda upp det certifikat som du har hämtat från Azure-portalen.

    e. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/origami-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/origami-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/origami-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/origami-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-origami-test-user"></a>Skapa en testanvändare Origami

I det här avsnittet skapar du en användare som kallas Britta Simon i Origami. 

1. Logga in på Origami-konto med administratörsrättigheter.

2. Klicka på menyn högst upp **Admin**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_51.png)

3. På den **användare och säkerhet** dialogrutan klickar du på **användare**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_54.png)

4. Klicka på **lägga till nya användare**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_55.png)

5. I dialogrutan Lägg till ny användare utför du följande steg:
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_56.png)

    a. I den **användarnamn** textruta ange e-postadress för användaren som **brittasimon@contoso.com**.

    b. I den **lösenord** textruta, ange ett lösenord.

    c. I den **Bekräfta lösenord** textruta, Skriv in lösenordet igen.

    d. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.

    e. I den **efternamn** textruta Ange efternamn för användaren som **Simon**.

    f. Klicka på **Spara**.
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_57.png)

6. Tilldela **användarroller** och **klientåtkomst** för användaren. 
   
    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Origami.

![Tilldela användare][200] 

**Om du vill tilldela Origami Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Origami**.

    ![Konfigurera enkel inloggning](./media/origami-tutorial/tutorial_origami_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Origami på åtkomstpanelen du bör få automatiskt loggat in på ditt Origami program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/origami-tutorial/tutorial_general_01.png
[2]: ./media/origami-tutorial/tutorial_general_02.png
[3]: ./media/origami-tutorial/tutorial_general_03.png
[4]: ./media/origami-tutorial/tutorial_general_04.png

[100]: ./media/origami-tutorial/tutorial_general_100.png

[200]: ./media/origami-tutorial/tutorial_general_200.png
[201]: ./media/origami-tutorial/tutorial_general_201.png
[202]: ./media/origami-tutorial/tutorial_general_202.png
[203]: ./media/origami-tutorial/tutorial_general_203.png

