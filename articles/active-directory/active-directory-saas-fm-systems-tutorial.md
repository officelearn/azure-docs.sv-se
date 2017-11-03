---
title: "Självstudier: Azure Active Directory-integrering med FM:Systems | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FM:Systems."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: 3a597d228f6c9234ec2fd2644ec3ac50b98f3b6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Självstudier: Azure Active Directory-integrering med FM:Systems

I kursen får lära du att integrera FM:Systems med Azure Active Directory (AD Azure).

Integrera FM:Systems med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FM:Systems
- Du kan aktivera användarna att automatiskt hämta loggat in på FM:Systems (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med FM:Systems, behöver du följande:

- En Azure AD-prenumeration
- En FM:Systems enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till FM:Systems från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fmsystems-from-the-gallery"></a>Att lägga till FM:Systems från galleriet
Du måste lägga till FM:Systems från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av FM:Systems i Azure AD.

**Utför följande steg för att lägga till FM:Systems från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **FM:Systems**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_search.png)

5. Välj i resultatpanelen **FM:Systems**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med FM:Systems baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i FM:Systems motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i FM:Systems upprättas.

I FM:Systems, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med FM:Systems, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FM:Systems](#creating-an-fmsystems-test-user)**  – du har en motsvarighet för Britta Simon i FM:Systems som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt FM:Systems program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FM:Systems:**

1. I Azure-portalen på den **FM:Systems** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_samlbase.png)

3. På den **FM:Systems domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_url.png)

    I den **Reply URL** textruta Skriv din FM:Systems **Reply URL**, Skriv URL-Adressen med följande mönster:`https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska Reply-URL. Kontakta [FM:Systems supportteamet](https://fmsystems.com/ask-us/) att hämta det här värdet.
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-fm-systems-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **FM:Systems** sida, måste du skicka den hämtade **XML-Metadata för** till [FM:Systems supportteamet](https://fmsystems.com/ask-us/). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor. Du får ett meddelande när enkel inloggning har aktiverats för din prenumeration.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-fmsystems-test-user"></a>Skapa en testanvändare FM:Systems

1. Logga in på webbplatsen FM:Systems företag som en administratör i ett webbläsarfönster.

2. Gå till **systemadministration \> Hantera säkerhet \> användare \> användarlistan**.
   
    ![Systemadministration](./media/active-directory-saas-fm-systems-tutorial/ic795905.png "systemadministration")

3. Klicka på **skapa nya användare**.
   
    ![Skapa ny användare](./media/active-directory-saas-fm-systems-tutorial/ic795906.png "Skapa ny användare")

4. I den **skapa användare** avsnittet, utför följande steg:
   
    ![Skapa användare](./media/active-directory-saas-fm-systems-tutorial/ic795907.png "skapa användare")
   
    a. Typ av **användarnamn**, **lösenord**, **Bekräfta lösenord**, **e-post** och **anställnings-ID** av en giltigt Azure Active Directory-konto som du vill etablera i relaterade textrutor.
   
    b. Klicka på **Nästa**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till FM:Systems.

![Tilldela användare][200] 

**Om du vill tilldela FM:Systems Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **FM:Systems**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen FM:Systems på åtkomstpanelen du bör få automatiskt loggat in på ditt FM:Systems program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_203.png

