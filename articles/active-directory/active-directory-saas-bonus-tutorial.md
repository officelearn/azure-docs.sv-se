---
title: 'Självstudier: Azure Active Directory-integrering med Bonusly | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 8133ec95bf0cbf6a9ce4ca1ad224d03ac593c940
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Självstudier: Azure Active Directory-integrering med Bonusly

I kursen får lära du att integrera Bonusly med Azure Active Directory (AD Azure).

Integrera Bonusly med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bonusly
- Du kan aktivera användarna att automatiskt hämta loggat in på Bonusly (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Bonusly, behöver du följande:

- En Azure AD-prenumeration
- En Bonusly enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Bonusly från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bonusly-from-the-gallery"></a>Att lägga till Bonusly från galleriet
Du måste lägga till Bonusly från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Bonusly i Azure AD.

**Utför följande steg för att lägga till Bonusly från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Bonusly**väljer **Bonusly** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Bonusly i resultatlistan](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Bonusly baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Bonusly motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Bonusly upprättas.

I Bonusly, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bonusly, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Bonusly testanvändare](#create-a-bonusly-test-user)**  – du har en motsvarighet för Britta Simon i Bonusly som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Bonusly program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bonusly:**

1. I Azure-portalen på den **Bonusly** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. På den **Bonusly domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och bonusly domän med enkel inloggning information](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    I den **Reply URL** textruta Skriv en URL med följande mönster: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med det faktiska Reply-URL. Kontakta [Bonusly supportteamet](https://Bonusly/contact) värdet hämtas.
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värde från certifikatet.

    ![Länken hämta certifikatet](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. På den **Bonusly Configuration** klickar du på **konfigurera Bonusly** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Bonusly konfiguration](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. I en annan webbläsare och logga in på ditt **Bonusly** klient.

8. Klicka på i verktygsfältet högst upp **inställningar**, och välj sedan **integreringar och appar**.
   
    ![Bonusly sociala avsnittet](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. Under **enkel inloggning**väljer **SAML**.

10. På den **SAML** dialogrutan utför följande steg:
   
    ![Bonusly Saml dialogrutan sidan](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. I den **IdP SSO mål-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
   
    b. I den **IdP utfärdaren** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen. 

    c. I den **IdP inloggnings-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    d. Klistra in den **tumavtrycket** kopieras värdet från Azure-portalen i den **Cert fingeravtryck** textruta.
   
11. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-bonusly-test-user"></a>Skapa en Bonusly testanvändare

För att aktivera Azure AD-användare kan logga in på Bonusly etableras de i Bonusly. När det gäller Bonusly är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda något annat Bonusly användarens konto skapas verktyg eller API: er som tillhandahålls av Bonusly att etablera AAD-användarkonton.
>  

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din Bonusly klient i ett webbläsarfönster.

2. Klicka på **inställningar**.
 
    ![Inställningar för](./media/active-directory-saas-bonus-tutorial/ic781041.png "inställningar")

3. Klicka på den **användare och tillägg** fliken.
   
    ![Användare och tillägg](./media/active-directory-saas-bonus-tutorial/ic781042.png "användare och tillägg")

4. Klicka på **hantera användare**.
   
    ![Hantera användare](./media/active-directory-saas-bonus-tutorial/ic781043.png "hantera användare")

5. Klicka på **lägga till användare**.
   
    ![Lägg till användare](./media/active-directory-saas-bonus-tutorial/ic781044.png "lägga till användare")

6. På den **Lägg till användare** dialogrutan, utför följande steg:
   
    ![Lägg till användare](./media/active-directory-saas-bonus-tutorial/ic781045.png "lägga till användare")  

    a. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textruta Ange efternamn för användaren som **Simon**.
 
    c. I den **e-post** textruta ange e-postadress för användaren som **brittasimon@contoso.com**.

    d. Klicka på **Spara**.
   
     >[!NOTE]
     >Azure AD-kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan den aktiveras.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bonusly.

![Tilldela rollen][200] 

**Om du vill tilldela Bonusly Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Bonusly**.

    ![Bonusly länken i listan med program](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Bonusly på panelen åtkomst du ska hämta automatiskt loggat in på ditt Bonusly program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png

