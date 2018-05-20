---
title: 'Självstudier: Azure Active Directory-integrering med Marketo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 48aee4565548fd57ef9925a41299b06660df2718
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Självstudier: Azure Active Directory-integrering med Marketo

I kursen får lära du att integrera Marketo med Azure Active Directory (AD Azure).

Integrera Marketo med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Marketo
- Du kan aktivera användarna att automatiskt hämta loggat in på Marketo (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Marketo, behöver du följande:

- En Azure AD-prenumeration
- En Marketo enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Marketo från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-marketo-from-the-gallery"></a>Att lägga till Marketo från galleriet
Du måste lägga till Marketo från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Marketo i Azure AD.

**Utför följande steg för att lägga till Marketo från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Marketo**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. Välj i resultatpanelen **Marketo**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Marketo baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Marketo motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Marketo upprättas.

I Marketo, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Marketo, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Marketo](#creating-a-marketo-test-user)**  – du har en motsvarighet för Britta Simon i Marketo som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Marketo-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Marketo:**

1. I Azure-portalen på den **Marketo** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. På den **Marketo domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://saml.marketo.com/sp`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Marketo supportteamet](http://investors.marketo.com/contactus.cfm) att hämta dessa värden.
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. På den **Marketo Configuration** klickar du på **konfigurera Marketo** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. Logga in på Marketo med administratörsautentiseringsuppgifter för att få Munchkin Id för ditt program, och utför följande åtgärder:
   
    a. Logga in på Marketo-app med administratörsautentiseringsuppgifter.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn integrering och på den **Munchkin länk**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopiera Munchkin-Id som visas på skärmen och slutför Reply-URL i guiden för konfiguration av Azure AD.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. Så här konfigurerar du SSO i programmet i nedanstående steg:
   
    a. Logga in på Marketo-app med administratörsautentiseringsuppgifter.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn integrering och på **enkel inloggning**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Om du vill aktivera SAML-inställningar, klickar du på **redigera** knappen.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Aktiverad** inställningar för enkel inloggning.
   
    f. Klistra in den **SAML enhets-ID**i den **utfärdaren ID** textruta.
   
    g. I den **enhets-ID** textruta ange URL: en som `http://saml.marketo.com/sp`.
   
    h. Välj den plats som ID **namnidentifierare elementet**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Om ditt användar-ID inte UPN-värde kan ändra värdet i fliken attribut.
   
    i. Ladda upp certifikatet som du har hämtat från Azure AD-konfigurationsguiden. **Spara** inställningarna.
   
    j. Redigera inställningar för omdirigera felsidor.
   
    k. Klistra in den **SAML enkel inloggning Tjänstwebbadress** i den **inloggnings-URL** textruta.
   
    l. Klistra in den **Sign-Out URL** i den **logga ut URL** textruta.
   
    m. I den **fel URL**, kopiera ditt **Marketo instans-URL** och på **spara** för att spara inställningar.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. Om du vill aktivera enkel inloggning för användare att utföra följande åtgärder:
   
    a. Logga in på Marketo-app med administratörsautentiseringsuppgifter.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigera till den **säkerhet** -menyn och klicka på **inloggningsinställningar**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Kontrollera den **kräver SSO** alternativet och **spara** inställningarna.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-marketo-test-user"></a>Skapa en testanvändare Marketo

I det här avsnittet skapar du en användare som kallas Britta Simon i Marketo. Följ dessa steg om du vill skapa en användare i Marketo-plattformen.

1. Logga in på Marketo-app med administratörsautentiseringsuppgifter.

2. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Navigera till den **säkerhet** -menyn och klicka på **användare och roller**
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. Klicka på den **bjuda in nya användare** länk på fliken användare
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. Fyll i följande information i guiden bjuda in nya användare
   
    a. Ange användaren **e-post** adressen i textrutan
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Ange den **Förnamn** i textrutan
   
    c. Ange den **efternamn** i textrutan
   
    d. Klicka på **Nästa**

6. I den **behörigheter** väljer den **userRoles** och på **nästa**
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Klicka på den **skicka** för att skicka användarinbjudan
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. Användaren får e-postmeddelande och klicka på länken och ändra lösenord för att aktivera kontot. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Marketo.

![Tilldela användare][200] 

**Om du vill tilldela Marketo Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Marketo**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Marketo på åtkomstpanelen du bör få automatiskt loggat in på ditt Marketo-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png

