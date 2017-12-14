---
title: "Självstudier: Azure Active Directory-integrering med Samanage | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Samanage."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 3236c12af214c7d27f1ac835b654b36819b5e80b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Självstudier: Azure Active Directory-integrering med Samanage

I kursen får lära du att integrera Samanage med Azure Active Directory (AD Azure).

Integrera Samanage med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Samanage
- Du kan aktivera användarna att automatiskt hämta loggat in på Samanage (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Samanage, behöver du följande:

- En Azure AD-prenumeration
- En Samanage enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Samanage från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-samanage-from-the-gallery"></a>Att lägga till Samanage från galleriet
Du måste lägga till Samanage från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Samanage i Azure AD.

**Utför följande steg för att lägga till Samanage från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Samanage**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_search.png)

5. Välj i resultatpanelen **Samanage**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Samanage baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Samanage motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Samanage upprättas.

I Samanage, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Samanage, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Samanage](#creating-a-samanage-test-user)**  – du har en motsvarighet för Britta Simon i Samanage som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Samanage program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Samanage:**

1. I Azure-portalen på den **Samanage** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_samlbase.png)

3. På den **Samanage domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare som beskrivs senare i självstudierna. För mer information kontaktar [Samanage klienten supportteamet](https://www.samanage.com/support).    
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samanage-tutorial/tutorial_general_400.png)

6. På den **Samanage Configuration** klickar du på **konfigurera Samanage** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_configure.png) 

7. Logga in på webbplatsen Samanage företag som en administratör i en annan webbläsarfönster.

8. Klicka på **instrumentpanelen** och välj **installationsprogrammet** i vänstra navigeringsfönstret.
   
    ![Instrumentpanelen](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "instrumentpanelen")

9. Klicka på **enkel inloggning**.
   
    ![Enkel inloggning](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "enkel inloggning")

10. Gå till **inloggningen med SAML** avsnittet, utför följande steg:
   
    ![Logga in med SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "inloggningen med SAML")
 
    a. Klicka på **aktivera enkel inloggning med SAML**.  
 
    b. I den **identitet providern URL** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.    
 
    c. Bekräfta de **inloggnings-URL** matchar den **logga URL** av **Samanage domän och URL: er** avsnitt i Azure-portalen.
 
    d. I den **logga ut URL** textruta anger du värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
 
    e. I den **SAML utfärdaren** textruta, ange URI för app-id som angetts i identitetsprovider.
 
    f. Öppna din Base64-kodade certifikat hämtas från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **klistra in din identitetsleverantör x.509-certifikatet nedan** textruta.
 
    g. Klicka på **skapa användare om de inte finns i Samanage**.
 
    h. Klicka på **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-samanage-test-user"></a>Skapa en testanvändare Samanage

Om du vill aktivera Azure AD-användare kan logga in på Samanage etableras de i Samanage.  
När det gäller Samanage är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Samanage företag som administratör.

2. Klicka på **instrumentpanelen** och välj **installationsprogrammet** i navigeringen till vänster Panorera.
   
    ![Installationsprogrammet](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "installationen")

3. Klicka på den **användare** fliken
   
    ![Användare](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "användare")

4. Klicka på **ny användare**.
   
    ![Ny användare](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "ny användare")

5. Typ av **namn** och **e-postadress** för ett Azure Active Directory-konto som du vill etablera och klicka på **skapa användare**.
   
    ![Skapa användare](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "skapa användare")
   
   >[!NOTE]
   >Innehavaren för Azure Active Directory-konto ett e-postmeddelande och länken för att bekräfta sina konton innan den aktiveras. Du kan använda något annat Samanage användarens konto skapas verktyg eller API: er som tillhandahålls av Samanage för att etablera Azure Active Directory användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Samanage.

![Tilldela användare][200] 

**Om du vill tilldela Samanage Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Samanage**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Samanage på åtkomstpanelen du bör få automatiskt loggat in på ditt Samanage program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_203.png

