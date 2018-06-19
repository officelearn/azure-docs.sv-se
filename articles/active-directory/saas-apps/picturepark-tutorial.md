---
title: 'Självstudier: Azure Active Directory-integrering med Picturepark | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: cd2caca48992d5fa3bec64004047ecd0fc2b2bfa
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966626"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Självstudier: Azure Active Directory-integrering med Picturepark

I kursen får lära du att integrera Picturepark med Azure Active Directory (AD Azure).

Integrera Picturepark med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Picturepark
- Du kan aktivera användarna att automatiskt hämta loggat in på Picturepark (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Picturepark, behöver du följande:

- En Azure AD-prenumeration
- En Picturepark enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Picturepark från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-picturepark-from-the-gallery"></a>Att lägga till Picturepark från galleriet
Du måste lägga till Picturepark från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Picturepark i Azure AD.

**Utför följande steg för att lägga till Picturepark från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Picturepark**.

    ![Skapa en testanvändare i Azure AD](./media/picturepark-tutorial/tutorial_picturepark_search.png)

5. Välj i resultatpanelen **Picturepark**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Picturepark baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Picturepark motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Picturepark upprättas.

I Picturepark, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Picturepark, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Picturepark](#creating-a-picturepark-test-user)**  – du har en motsvarighet för Britta Simon i Picturepark som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Picturepark program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Picturepark:**

1. I Azure-portalen på den **Picturepark** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. På den **Picturepark domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.picturepark.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Picturepark klienten supportteamet](https://picturepark.com/about/contact/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_general_400.png)

6. På den **Picturepark Configuration** klickar du på **konfigurera Picturepark** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

7. Logga in på webbplatsen Picturepark företag som en administratör i en annan webbläsarfönster.

8. Klicka på i verktygsfältet högst upp **Administrationsverktyg**, och klicka sedan på **hanteringskonsolen**.
   
    ![Konsolen för hantering av](./media/picturepark-tutorial/ic795062.png "Management Console")

9. Klicka på **autentisering**, och klicka sedan på **identitetsleverantörer**.
   
    ![Autentisering](./media/picturepark-tutorial/ic795063.png "autentisering")

10. I den **identitet providerkonfigurationen** avsnittet, utför följande steg:
   
    ![Identitet providerkonfigurationen](./media/picturepark-tutorial/ic795064.png "identitet providerkonfigurationen")
   
    a. Klicka på **Lägg till**.
  
    b. Ange ett namn för din konfiguration.
   
    c. Välj **anges som standard**.
   
    d. I **Issuer URI** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    e. I **betrodda utfärdare USB utskrifts** textruta klistra in värdet för **tumavtrycket** som du har kopierat från **SAML-signeringscertifikat** avsnitt. 

11. Klicka på **JoinDefaultUsersGroup**.

12. Ange den **e-postadress** attribut i den **anspråk** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` och klicka på **spara**.

      ![Konfigurationen](./media/picturepark-tutorial/ic795065.png "konfiguration")

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/picturepark-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/picturepark-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/picturepark-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-picturepark-test-user"></a>Skapa en testanvändare Picturepark

För att aktivera Azure AD-användare att logga in på Picturepark etableras de i Picturepark. När det gäller Picturepark är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Picturepark** klient.

2. Klicka på i verktygsfältet högst upp **Administrationsverktyg**, och klicka sedan på **användare**.
   
    ![Användare](./media/picturepark-tutorial/ic795067.png "användare")

3. I den **översikt över användare** klickar du på **ny**.
   
    ![Användarhantering](./media/picturepark-tutorial/ic795068.png "användarhantering")

4. På den **skapa användare** dialogrutan, utför följande steg på en giltig Azure Active Directory-användare du vill att tillhandahålla:
   
    ![Skapa användare](./media/picturepark-tutorial/ic795069.png "skapa användare")
   
    a. I den **e-postadress** textruta typ av **e-postadress** användarens **BrittaSimon@contoso.com**.  
   
    b. I den **lösenord** och **Bekräfta lösenord** textrutor, typen av **lösenord** av BrittaSimon. 
   
    c. I den **Förnamn** textruta typ av **Förnamn** användarens **Britta**. 
   
    d. I den **efternamn** textruta typ av **efternamn** användarens **Simon**.
   
    e. I den **företagets** textruta typ av **företagsnamn** för användaren. 
   
    f. I den **land** textruta väljer den **land** för användaren.
  
    g. I den **ZIP** textruta typ av **postnummer** orten.
   
    h. I den **Stad** textruta typ av **Ortnamn** för användaren.

    i. Välj en **språk**.
   
    j. Klicka på **Skapa**.

>[!NOTE]
>Du kan använda något annat Picturepark användarens konto skapas verktyg eller API: er som tillhandahålls av Picturepark att etablera Azure AD-användarkonton.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Picturepark.

![Tilldela användare][200] 

**Om du vill tilldela Picturepark Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Picturepark**.

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Picturepark på åtkomstpanelen du bör få automatiskt loggat in på ditt Picturepark program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/picturepark-tutorial/tutorial_general_01.png
[2]: ./media/picturepark-tutorial/tutorial_general_02.png
[3]: ./media/picturepark-tutorial/tutorial_general_03.png
[4]: ./media/picturepark-tutorial/tutorial_general_04.png

[100]: ./media/picturepark-tutorial/tutorial_general_100.png

[200]: ./media/picturepark-tutorial/tutorial_general_200.png
[201]: ./media/picturepark-tutorial/tutorial_general_201.png
[202]: ./media/picturepark-tutorial/tutorial_general_202.png
[203]: ./media/picturepark-tutorial/tutorial_general_203.png

