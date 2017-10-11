---
title: "Självstudier: Azure Active Directory-integrering med Ceridian Dayforce HCM | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ceridian Dayforce HCM."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b2ea3d92f233dab5bd6814e4875f881117eac8e3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Självstudier: Azure Active Directory-integrering med Ceridian Dayforce HCM

I kursen får lära du att integrera Ceridian Dayforce HCM med Azure Active Directory (AD Azure).

Integrera Ceridian Dayforce HCM med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Ceridian Dayforce HCM.
- Du kan aktivera användarna att automatiskt hämta loggat in på Ceridian Dayforce HCM (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Ceridian Dayforce HCM behöver du följande:

- En Azure AD-prenumeration
- En Ceridian Dayforce HCM enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Ceridian Dayforce HCM från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Att lägga till Ceridian Dayforce HCM från galleriet
Du måste lägga till Ceridian Dayforce HCM från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Ceridian Dayforce HCM i Azure AD.

**Utför följande steg för att lägga till Ceridian Dayforce HCM från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Ceridian Dayforce HCM**väljer **Ceridian Dayforce HCM** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Ceridian Dayforce HCM i resultatlistan](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Ceridian Dayforce HCM baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Ceridian Dayforce HCM motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Ceridian Dayforce HCM upprättas.

I Ceridian Dayforce HCM tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Ceridian Dayforce HCM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Ceridian Dayforce HCM](#create-a-ceridian-dayforce-hcm-test-user)**  – har en motsvarighet för Britta Simon Ceridian Dayforce HCM som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Ceridian Dayforce HCM-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Ceridian Dayforce HCM:**

1. I Azure-portalen på den **Ceridian Dayforce HCM** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_samlbase.png)

3. På den **Ceridian Dayforce HCM domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_url.png)
    
    a. I den **logga URL** textruta, Skriv URL: en som används av dina användare logga in i tillämpningsprogrammet Ceridian Dayforce HCM.
    
    | Miljö | URL: EN |
    | :-- | :-- |
    | För produktion | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | För testet | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |
    
    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    
    | Miljö | URL: EN |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp` |
    | För testet | `https://fs-test.dayforcehcm.com/sp` |
    
    c. I den **Reply URL** textruta, Skriv URL-Adressen används av Azure AD publicera svaret.
    
    | Miljö | URL: EN |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | För testet | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska identifierare, Reply URL och inloggnings-URL. Kontakta [Ceridian Dayforce HCM klienten supportteamet](https://www.ceridian.com/contact-us/index.html) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_certificate.png) 

5. Tillämpningsprogrammet Ceridian Dayforce HCM förväntar SAML-intyg i ett specifikt format. Arbeta med [Ceridian Dayforce HCM supportteamet](https://www.ceridian.com/contact-us/index.html) först för att identifiera rätt användar-ID. Microsoft rekommenderar att du använder den **”name”** attribut som användaridentifierare. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.  

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributets namn  | Attributvärdet |
    | --------------- | -------------------- |    
    | namn  | User.extensionattribute2 |    

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. I den **värdet** väljer användarattribut som du vill använda för din implementering.
    Till exempel om du vill använda EmployeeID som unikt identifierare och du har lagrat attributvärdet i ExtensionAttribute2, välj sedan **user.extensionattribute2**.
    
    d. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_400.png)
    
8. På den **Ceridian Dayforce HCM Configuration** klickar du på **konfigurera Ceridian Dayforce HCM** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Ceridian Dayforce HCM-konfiguration](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_configure.png) 

9. Konfigurera enkel inloggning på **Ceridian Dayforce HCM** sida, måste du skicka den hämtade **XML-Metadata för** och **Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** till [Ceridian Dayforce HCM supportteamet](https://www.ceridian.com/contact-us/index.html).

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-ceridian-dayforce-hcm-test-user"></a>Skapa en Ceridian Dayforce HCM testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Ceridian Dayforce HCM. Arbeta med den [Ceridian Dayforce HCM supportteamet](https://www.ceridian.com/contact-us/index.html) att hämta användare som lagts till i Ceridian Dayforce HCM-programmet. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ceridian Dayforce HCM.

![Tilldela användare][200] 

**Om du vill tilldela Ceridian Dayforce HCM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Ceridian Dayforce HCM**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ceridian Dayforce HCM.

![Tilldela rollen][200] 

**Om du vill tilldela Ceridian Dayforce HCM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Ceridian Dayforce HCM**.

    ![Länken Ceridian Dayforce HCM i listan med program](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.  
När du klickar på panelen Ceridian Dayforce HCM på åtkomstpanelen du bör få automatiskt loggat in på ditt Ceridian Dayforce HCM-program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png

