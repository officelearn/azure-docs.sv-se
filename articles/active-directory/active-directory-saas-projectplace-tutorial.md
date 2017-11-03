---
title: "Självstudier: Azure Active Directory-integrering med Projectplace | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Projectplace."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: bb9dd10c887cb0e42e544066d9b0dcfa554e10ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Självstudier: Azure Active Directory-integrering med Projectplace

I kursen får lära du att integrera Projectplace med Azure Active Directory (AD Azure).

Integrera Projectplace med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Projectplace
- Du kan aktivera användarna att automatiskt hämta loggat in på Projectplace (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Projectplace, behöver du följande:

- En Azure AD-prenumeration
- En Projectplace enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Projectplace från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-projectplace-from-the-gallery"></a>Att lägga till Projectplace från galleriet
Du måste lägga till Projectplace från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Projectplace i Azure AD.

**Utför följande steg för att lägga till Projectplace från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Projectplace**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_search.png)

5. Välj i resultatpanelen **Projectplace**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Projectplace baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Projectplace motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Projectplace upprättas.

I Projectplace, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Projectplace, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Projectplace](#creating-a-projectplace-test-user)**  – du har en motsvarighet för Britta Simon i Projectplace som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Projectplace-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Projectplace:**

1. I Azure-portalen på den **Projectplace** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_samlbase.png)

3. På den **Projectplace-domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<company>.projectplace.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Projectplace klienten supportteamet](https://success.planview.com/Projectplace/Support) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-projectplace-tutorial/tutorial_general_400.png)

7. Konfigurera enkel inloggning på **Projectplace** sida, måste du skicka den hämtade **XML-Metadata för** till [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

>[!NOTE]
>Konfigurationen för enkel inloggning måste utföras av den [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support). Du får ett meddelande så snart som konfigurationen har slutförts.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-projectplace-test-user"></a>Skapa en Projectplace-testanvändare

För att aktivera Azure AD-användare att logga in på Projectplace etableras de i Projectplace. När det gäller Projectplace är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Projectplace** företagets webbplats som administratör.

2. Gå till **personer**, och klicka sedan på **medlemmar**.
   
    ![Personer](./media/active-directory-saas-projectplace-tutorial/ic790228.png "personer")

3. Klicka på **lägga till medlem**.
   
    ![Lägga till medlemmar](./media/active-directory-saas-projectplace-tutorial/ic790232.png "lägga till medlemmar")

4. I den **Lägg till medlem** avsnittet, utför följande steg:
   
    ![Nya medlemmar](./media/active-directory-saas-projectplace-tutorial/ic790233.png "nya medlemmar")
   
    a. I den **nya medlemmar** textruta, ange en giltig AAD-konto som du vill etablera i relaterade textrutor e-postadress.
   
    b. Klicka på **skicka**.

   Ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv skickas till innehavare för Azure Active Directory-konto.

>[!NOTE]
>Du kan använda något annat Projectplace användarens konto skapas verktyg eller API: er som tillhandahålls av Projectplace att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Projectplace.

![Tilldela användare][200] 

**Om du vill tilldela Projectplace Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Projectplace**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Projectplace på åtkomstpanelen du bör få automatiskt loggat in på ditt Projectplace-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_203.png

