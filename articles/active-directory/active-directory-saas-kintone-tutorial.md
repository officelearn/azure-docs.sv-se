---
title: "Självstudier: Azure Active Directory-integrering med Kintone | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kintone."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: e5e847c12cba3611ce7ea2c3e956dbd55b1e0cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Självstudier: Azure Active Directory-integrering med Kintone

I kursen får lära du att integrera Kintone med Azure Active Directory (AD Azure).

Integrera Kintone med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Kintone
- Du kan aktivera användarna att automatiskt hämta loggat in på Kintone (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Kintone, behöver du följande:

- En Azure AD-prenumeration
- En Kintone enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Kintone från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-kintone-from-the-gallery"></a>Att lägga till Kintone från galleriet
Du måste lägga till Kintone från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Kintone i Azure AD.

**Utför följande steg för att lägga till Kintone från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Kintone**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_search.png)

5. Välj i resultatpanelen **Kintone**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Kintone baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Kintone motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Kintone upprättas.

I Kintone, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kintone, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Kintone](#creating-a-kintone-test-user)**  – du har en motsvarighet för Britta Simon i Kintone som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Kintone program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Kintone:**

1. I Azure-portalen på den **Kintone** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_samlbase.png)

3. På den **Kintone domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<companyname>.kintone.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Kintone klienten supportteamet](https://www.kintone.com/contact/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-kintone-tutorial/tutorial_general_400.png)

6. På den **Kintone Configuration** klickar du på **konfigurera Kintone** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_configure.png) 

7. Logga in i ett annat webbläsarfönster din **Kintone** företagets webbplats som administratör.

8. Klicka på **inställningar**.
   
    ![Inställningar för](./media/active-directory-saas-kintone-tutorial/ic785879.png "inställningar")

9. Klicka på **användare & systemadministration**.
   
    ![Användare och systemadministration](./media/active-directory-saas-kintone-tutorial/ic785880.png "användare & systemadministration")

10. Under **systemadministration \> säkerhet** klickar du på **inloggning**.
   
    ![Inloggningen](./media/active-directory-saas-kintone-tutorial/ic785881.png "inloggning")

11. Klicka på **aktivera SAML-autentisering**.
   
    ![SAML-autentisering](./media/active-directory-saas-kintone-tutorial/ic785882.png "SAML-autentisering")

12. Utför följande steg i avsnittet SAML-autentisering:
    
    ![SAML-autentisering](./media/active-directory-saas-kintone-tutorial/ic785883.png "SAML-autentisering")
    
    a. I den **Inloggningswebbadressen** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    b. I den **logga ut URL** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
    
    c. Klicka på **Bläddra** att överföra din hämtat certifikat.
    
    d. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-kintone-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-kintone-test-user"></a>Skapa en testanvändare Kintone

Om du vill aktivera Azure AD-användare kan logga in på Kintone etableras de i Kintone.  
När det gäller Kintone är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg om du vill konfigurera ett användarkonto:

1. Logga in på ditt **Kintone** företagets webbplats som administratör.

2. Klicka på **inställningen**.
   
    ![Inställningar för](./media/active-directory-saas-kintone-tutorial/ic785879.png "inställningar")

3. Klicka på **användare & systemadministration**.
   
    ![Användaren & systemadministration](./media/active-directory-saas-kintone-tutorial/ic785880.png "systemadministration & användare")

4. Under **Användaradministration**, klickar du på **avdelningar och användare**.
   
    ![Avdelning och användare](./media/active-directory-saas-kintone-tutorial/ic785888.png "avdelning och användare")

5. Klicka på **ny användare**.
   
    ![Nya användare](./media/active-directory-saas-kintone-tutorial/ic785889.png "nya användare")

6. I den **ny användare** avsnittet, utför följande steg:
   
    ![Nya användare](./media/active-directory-saas-kintone-tutorial/ic785890.png "nya användare")
   
    a. Ange en **visningsnamn**, **inloggningsnamnet**, **nytt lösenord**, **Bekräfta lösenord**, **e-postadress**, och annan information om en giltig AAD-konto som du vill att etablera i relaterade textrutor.
 
    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda något annat Kintone användarens konto skapas verktyg eller API: er som tillhandahålls av Kintone att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kintone.

![Tilldela användare][200] 

**Om du vill tilldela Kintone Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Kintone**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Kintone på åtkomstpanelen du bör få automatiskt loggat in på ditt Kintone program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_203.png

