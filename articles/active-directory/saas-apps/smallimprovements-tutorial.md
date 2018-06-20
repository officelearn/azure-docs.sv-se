---
title: 'Självstudier: Azure Active Directory-integrering med små förbättringar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och mindre förbättringar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5a984c7955218e29e4a6f99c93e9336e88c9f63a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225838"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Självstudier: Azure Active Directory-integrering med små förbättringar

I kursen får lära du att integrera små förbättringar med Azure Active Directory (AD Azure).

Integrera små förbättringar med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till små förbättringar
- Du kan aktivera användarna att automatiskt hämta loggat in på små förbättringar (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med små förbättringar, behöver du följande:

- En Azure AD-prenumeration
- En liten förbättringar enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till små förbättringar från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-small-improvements-from-the-gallery"></a>Att lägga till små förbättringar från galleriet
Du måste lägga till små förbättringar från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av små förbättringar i Azure AD.

**Utför följande steg för att lägga till små förbättringar från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **små förbättringar**.

    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

5. Välj i resultatpanelen **små förbättringar**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med små förbättringar som baseras på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i små förbättringar motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i små förbättringar upprättas.

I små förbättringar, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med små förbättringar, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare små förbättringar](#creating-a-small-improvements-test-user)**  – du har en motsvarighet för Britta Simon i små förbättringar som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för små förbättringar.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med små förbättringar:**

1. I Azure-portalen på den **små förbättringar** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

3. På den **mindre förbättringar domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.small-improvements.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Small förbättringar Client supportteamet](mailto:support@small-improvements.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_general_400.png)

6. På den **små förbättringar Configuration** klickar du på **konfigurera små förbättringar** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

7. I ett nytt webbläsarfönster inloggning till webbplatsen förbättringar av små företag som administratör.

8. Huvudinstrumentpanelen-sidan klickar du på **Administration** knappen till vänster.
   
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

9. Klicka på den **SAML SSO** knappen från **integreringar** avsnitt.
   
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

10. Utför följande steg på konfigurationssidan för enkel inloggning:
   
    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. I den **HTTP-slutpunkten** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    b. Öppna din hämtat certifikat i anteckningar, kopiera innehållet och klistrar in det i den **x509 certifikat** textruta. 

    c. Om du vill ha enkel inloggning och inloggning formuläret autentiseringsalternativet tillgänglig för användare, kontrollera den **Aktivera åtkomst via inloggning för** alternativet.  

    d. Ange rätt värde för att namnge knappen SSO-inloggning i den **SAML fråga** textruta.  

    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-small-improvements-test-user"></a>Skapa en liten förbättringar testanvändare

Om du vill aktivera Azure AD-användare kan logga in på små förbättringar, måste de etableras i små förbättringar. För små förbättringar är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Inloggning på webbplatsen förbättringar av små företag som administratör.

2. Från sidan, gå till menyn till vänster, klickar på **Administration**.

3. Klicka på den **användarkatalog** knappen från Användarhantering avsnitt. 
   
    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

4. Klicka på **lägga till användare**.

    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

5. På den **Lägg till användare** dialogrutan, utför följande steg: 

    ![Skapa en testanvändare i Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Ange den **Förnamn** för användare som **Britta**.

    b. Ange den **efternamn** för användare som **Simon**.

    c. Ange den **e-post** för användare som **brittasimon@contoso.com**. 

    d. Du kan också välja att ange personligt meddelande i den **skicka e-postmeddelande** rutan. Om du inte vill skicka meddelandet avmarkerar du kryssrutan.

    e. Klicka på **skapa användare**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till små förbättringar.

![Tilldela användare][200] 

**Om du vill tilldela små förbättringar Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **små förbättringar**.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen små förbättringar på åtkomstpanelen du bör få automatiskt loggat in på ditt program för små förbättringar.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

