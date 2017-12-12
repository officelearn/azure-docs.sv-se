---
title: "Självstudier: Azure Active Directory-integrering med Freshservice | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshservice."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 9362c68ea2c05c231921daaa0557c454b5d28d0b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Självstudier: Azure Active Directory-integrering med Freshservice

I kursen får lära du att integrera Freshservice med Azure Active Directory (AD Azure).

Integrera Freshservice med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Freshservice
- Du kan aktivera användarna att automatiskt hämta loggat in på Freshservice (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Freshservice, behöver du följande:

- En Azure AD-prenumeration
- En Freshservice enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Freshservice från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-freshservice-from-the-gallery"></a>Att lägga till Freshservice från galleriet
Du måste lägga till Freshservice från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Freshservice i Azure AD.

**Utför följande steg för att lägga till Freshservice från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Freshservice**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_search.png)

5. Välj i resultatpanelen **Freshservice**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Freshservice baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Freshservice motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Freshservice upprättas.

I Freshservice, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Freshservice, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Freshservice](#creating-a-freshservice-test-user)**  – du har en motsvarighet för Britta Simon i Freshservice som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Freshservice program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Freshservice:**

1. I Azure-portalen på den **Freshservice** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_samlbase.png)

3. På den **Freshservice domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<democompany>.freshservice.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Freshservice klienten supportteamet](https://support.freshservice.com/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera **TUMAVTRYCKET** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshservice-tutorial/tutorial_general_400.png)

6. På den **Freshservice Configuration** klickar du på **konfigurera Freshservice** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen Freshservice företag som administratör.

8. Klicka på menyn högst upp **Admin**.
   
    ![Admin](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Admin")

9. I den **Kundportal**, klickar du på **säkerhet**.
   
    ![Säkerhet](./media/active-directory-saas-freshservice-tutorial/ic790815.png "säkerhet")

10. I den **säkerhet** avsnittet, utför följande steg:
   
    ![Enkel inloggning](./media/active-directory-saas-freshservice-tutorial/ic790816.png "enkel inloggning")
   
    a. Växeln **enkel inloggning**.

    b. Välj **SAML SSO**.

    c. I den **inloggnings-URL för SAML** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    d. I den **logga ut URL** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.

    e. I **säkerhet certifikat fingeravtryck** textruta klistra in den **TUMAVTRYCKET** värdet för certifikat som du har kopierat från Azure-portalen.

    f. Klicka på **spara**
   
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-freshservice-test-user"></a>Skapa en testanvändare Freshservice

Om du vill aktivera Azure AD-användare kan logga in på FreshService etableras de i FreshService. När det gäller FreshService är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **FreshService** företagets webbplats som administratör.

2. Klicka på menyn högst upp **Admin**.
   
    ![Admin](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Admin")

3. I den **Användarhantering** klickar du på **beställare**.
   
    ![Beställare](./media/active-directory-saas-freshservice-tutorial/ic790818.png "beställare")

4. Klicka på **ny frågeställare**.
   
    ![Nya beställare](./media/active-directory-saas-freshservice-tutorial/ic790819.png "nya beställare")

5. I den **ny frågeställare** avsnittet, utför följande steg:
   
    ![Ny frågeställare](./media/active-directory-saas-freshservice-tutorial/ic790820.png "ny frågeställare")   

    a. Ange den **Förnamn** och **e-post** attribut för ett giltigt Azure Active Directory-konto som du vill etablera i relaterade textrutor.

    b. Klicka på **Spara**.
   
    >[!NOTE]
    >Azure Active Directory kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv
    >  

>[!NOTE]
>Du kan använda något annat FreshService användarens konto skapas verktyg eller API: er som tillhandahålls av FreshService att etablera AAD-användarkonton.
>  

![Tilldela användare][200] 

**Om du vill tilldela Freshservice Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Freshservice**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Freshservice på åtkomstpanelen du bör få automatiskt loggat in på ditt Freshservice program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_203.png

