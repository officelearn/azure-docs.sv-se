---
title: "Självstudier: Azure Active Directory-integrering med BlueJeans | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BlueJeans."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: jeedes
ms.openlocfilehash: 03bf65852b8d3cf14aebf155891a028db86e78d0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Självstudier: Azure Active Directory-integrering med BlueJeans

I kursen får lära du att integrera BlueJeans med Azure Active Directory (AD Azure).

Integrera BlueJeans med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BlueJeans
- Du kan aktivera användarna att automatiskt hämta loggat in på BlueJeans (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med BlueJeans, behöver du följande:

- En Azure AD-prenumeration
- En BlueJeans enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till BlueJeans från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bluejeans-from-the-gallery"></a>Att lägga till BlueJeans från galleriet
Du måste lägga till BlueJeans från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av BlueJeans i Azure AD.

**Utför följande steg för att lägga till BlueJeans från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **BlueJeans**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_search.png)

5. Välj i resultatpanelen **BlueJeans**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med BlueJeans baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i BlueJeans motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i BlueJeans upprättas.

I BlueJeans, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med BlueJeans, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare BlueJeans](#creating-a-bluejeans-test-user)**  – du har en motsvarighet för Britta Simon i BlueJeans som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt BlueJeans program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med BlueJeans:**

1. I Azure-portalen på den **BlueJeans** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

3. På den **BlueJeans domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<companyname>.BlueJeans.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<companyname>.BlueJeans.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [BlueJeans klienten supportteamet](https://support.bluejeans.com/contact) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-bluejeans-tutorial/tutorial_general_400.png)

6. På den **BlueJeans Configuration** klickar du på **konfigurera BlueJeans** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, ändra lösenord URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_configure.png) 

7. I en annan webbläsarfönstret, logga in på ditt **BlueJeans** företagets webbplats som administratör.

8. Gå till **ADMIN \> gruppinställningarna \> säkerhet**.
   
   ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

9. I den **säkerhet** avsnittet, utför följande steg:
   
   ![SAML för enkel inloggning](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML för enkel inloggning")   
   
   a. Välj **SAML för enkel inloggning**.
  
   b. Välj **aktivera automatisk etablering**.

10. Gå vidare med följande steg:

    ![Certifikat sökvägen](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "certifikat sökväg")
    
    a. Klicka på **Välj fil**, och sedan ladda upp hämtat certifikat.
   
    b. Klistra in **SAML enkel inloggning Tjänstwebbadress** till den **inloggnings-URL** textruta.
   
    c. Klistra in **ändra lösenord URL** till den **lösenord ändra URL** textruta.
   
    d. Klistra in **Sign-Out URL** till den **logga ut URL** textruta.

11. Gå vidare med följande steg:
    
    ![Spara ändringarna](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "spara ändringar")
    
    a. I den **användar-id** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    b. I den **e-post** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    c. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-bluejeans-test-user"></a>Skapa en testanvändare BlueJeans

Om du vill aktivera Azure AD-användare kan logga in på BlueJeans etableras de i BlueJeans.  

Vid BlueJeans är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på ditt **BlueJeans** företagets webbplats som administratör.

2. Gå till **ADMIN \> hantera användare \> lägga till användare**.
   
   ![Admin](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   >[!IMPORTANT]
   >Den **Lägg till användare** fliken är endast tillgänglig om i den **säkerhetsfliken**, **aktivera automatisk etablering** är avmarkerad. 
   
3. I den **Lägg till användare** avsnittet, utför följande steg:

    ![Lägg till användare](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "lägga till användare")
    
    a. Ange en **BlueJeans användarnamn**, en **e-postadress**, **BlueJeans möte ID**, **kontrollant lösenord**, en **fullständigt namn** , **företagets** av en giltig AAD-konto som du vill etablera i relaterade textrutor.
    
    b. Klicka på **lägga till användare**.

>[!NOTE]
>Du kan använda något annat BlueJeans användarens konto skapas verktyg eller API: er som tillhandahålls av BlueJeans att etablera AAD-användarkonton. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BlueJeans.

![Tilldela användare][200] 

**Om du vill tilldela BlueJeans Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **BlueJeans**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Du bör få inloggningssidan för BlueJeans program när du klickar på panelen BlueJeans på åtkomstpanelen.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_203.png

