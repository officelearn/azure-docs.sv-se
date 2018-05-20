---
title: 'Självstudier: Azure Active Directory-integrering med Citrix ShareFile | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 0e860c1f1db77026e775191c76350333aa66dc7f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Självstudier: Azure Active Directory-integrering med Citrix ShareFile

I kursen får lära du att integrera Citrix ShareFile med Azure Active Directory (AD Azure).

Integrera Citrix ShareFile med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Citrix ShareFile.
- Du kan aktivera användarna att automatiskt hämta loggat in på Citrix ShareFile (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Citrix ShareFile behöver du följande:

- En Azure AD-prenumeration
- En Citrix ShareFile enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till Citrix ShareFile från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-citrix-sharefile-from-the-gallery"></a>Lägg till Citrix ShareFile från galleriet
Du måste lägga till Citrix ShareFile från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Citrix ShareFile i Azure AD.

**Utför följande steg för att lägga till Citrix ShareFile från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Citrix ShareFile**väljer **Citrix ShareFile** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Citrix ShareFile i resultatlistan](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med Citrix ShareFile baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Citrix ShareFile motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Citrix ShareFile upprättas.

I Citrix ShareFile tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Citrix ShareFile, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Citrix ShareFile](#create-a-citrix-sharefile-test-user)**  – du har en motsvarighet för Britta Simon i Citrix ShareFile som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Citrix ShareFile-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Citrix ShareFile:**

1. I Azure-portalen på den **Citrix ShareFile** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. På den **Citrix ShareFile domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Citrix ShareFile domän med enkel inloggning information](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenant-name>.sharefile.com/saml/login`

    b. I den **identifierare (enhets-ID)** textruta Skriv en URL med följande mönster:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. I den **Reply URL** textruta Skriv en URL med följande mönster:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och Reply-URL. Kontakta [Citrix ShareFile klienten supportteamet](https://www.citrix.co.in/products/sharefile/support.html) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. På den **Citrix-konfiguration för ShareFile** klickar du på **konfigurera Citrix ShareFile** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Citrix ShareFile konfiguration](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png)

7. Logga in i ett annat webbläsarfönster din **Citrix ShareFile** företagets webbplats som administratör.

8. Klicka på i verktygsfältet högst upp **Admin**.

9. I det vänstra navigeringsfönstret väljer **Konfigurera enkel inloggning**.
   
    ![Kontot Administration](./media/active-directory-saas-sharefile-tutorial/ic773627.png "konto Administration")

10. På den **enkel inloggning / SAML 2.0-konfiguration** dialogrutan sidan under **grundläggande inställningar**, utför följande steg:
   
    ![Enkel inloggning](./media/active-directory-saas-sharefile-tutorial/ic773628.png "enkel inloggning")
   
    a. Klicka på **aktivera SAML**.
    
    b. I **din IDP utfärdaren / enhets-ID** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    c. Klicka på **ändra** bredvid den **X.509-certifikat** fältet och sedan ladda upp det certifikat du laddade ned från Azure-portalen.
    
    d. I **Inloggningswebbadressen** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
    
    e. I **logga ut URL** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.

11. Klicka på **spara** i Citrix ShareFile management portal.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Skapa en testanvändare Citrix ShareFile

För att aktivera Azure AD-användare kan logga in Citrix ShareFile etableras de i Citrix ShareFile. Citrix ShareFile är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Citrix ShareFile** klient.

2. Klicka på **hantera användare \> hantera användare Home \> + skapa medarbetare**.
   
   ![Skapa medarbetare](./media/active-directory-saas-sharefile-tutorial/IC781050.png "skapa medarbetare")

3. På den **grundläggande Information** avsnittet, utföra nedanstående steg:
   
   ![Grundläggande Information](./media/active-directory-saas-sharefile-tutorial/IC799951.png "grundläggande Information")
   
   a. I den **e-postadress** textruta skriver Britta Simon som e-postadress **brittasimon@contoso.com**.
   
   b. I den **Förnamn** textruta typen **Förnamn** för användare som **Britta**.
   
   c. I den **efternamn** textruta typen **efternamn** för användare som **Simon**.

4. Klicka på **lägga till användare**.
  
   >[!NOTE]
   >Azure AD-kontoinnehavaren kommer ett e-postmeddelande och länken för att bekräfta sina konton innan den aktiveras. Du kan använda andra Citrix ShareFile användare skapa verktyg eller API: er som tillhandahålls av Citrix ShareFile för att etablera Azure AD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Citrix ShareFile.

![Tilldela rollen][200] 

**Om du vill tilldela Citrix ShareFile Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Citrix ShareFile**.

    ![Länken Citrix ShareFile i listan med program](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Citrix ShareFile på åtkomstpanelen du bör få automatiskt loggat in på ditt Citrix ShareFile-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
