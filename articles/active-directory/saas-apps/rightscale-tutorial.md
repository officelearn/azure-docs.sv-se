---
title: 'Självstudier: Azure Active Directory-integrering med Rightscale | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: af6b80b7b38a612ac21283991c601ebad72fecde
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963913"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Självstudier: Azure Active Directory-integrering med Rightscale

I kursen får lära du att integrera Rightscale med Azure Active Directory (AD Azure).

Integrera Rightscale med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Rightscale
- Du kan aktivera användarna att automatiskt hämta loggat in på Rightscale (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Rightscale, behöver du följande:

- En Azure AD-prenumeration
- En Rightscale enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Rightscale från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-rightscale-from-the-gallery"></a>Att lägga till Rightscale från galleriet
Du måste lägga till Rightscale från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Rightscale i Azure AD.

**Utför följande steg för att lägga till Rightscale från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Rightscale**.

    ![Skapa en testanvändare i Azure AD](./media/rightscale-tutorial/tutorial_rightscale_search.png)

5. Välj i resultatpanelen **Rightscale**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Rightscale baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Rightscale motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Rightscale upprättas.

I Rightscale, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Rightscale, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Rightscale](#creating-a-rightscale-test-user)**  – du har en motsvarighet för Britta Simon i Rightscale som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Rightscale program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Rightscale:**

1. I Azure-portalen på den **Rightscale** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

3. På den **Rightscale domän och URL: er** om du vill konfigurera programmet i **IDP initierade läge** du behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_url.png)

4. På den **Rightscale domän och URL: er** om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Klicka på den **visa avancerade inställningar för URL: en**.

    b. I den **logga URL** textruta anger du URL: `https://login.rightscale.com/`

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_general_400.png)

7. På den **Rightscale Configuration** klickar du på **konfigurera Rightscale** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
8. För att få SSO konfigurerats för ditt program måste logga in till din RightScale-klient som administratör.

    a. Klicka på menyn högst upp i **inställningar** fliken och markera **enkel inloggning**.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Klicka på ”**nya**” för att lägga till **din SAML identitetsleverantörer**.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. I textrutan för **visningsnamn**, ange företagets namn.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Välj **Tillåt RightScale-initierad SSO med hjälp av en identifiering ledtråd** och inkommande din **domännamn** i den under textrutan.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. Klistra in värdet för **SAML enkel inloggning Tjänstwebbadress** som du har kopierat från Azure-portalen i **SAML SSO Endpoint** i RightScale.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. Klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen i **ID för SAML entiteterna** i RightScale.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. Klicka på **webbläsare** för att ladda upp certifikatet som du laddade ned från Azure-portalen.
   
    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. Klicka på **Spara**.
<CE>
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/rightscale-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/rightscale-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/rightscale-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-rightscale-test-user"></a>Skapa en testanvändare Rightscale

I det här avsnittet skapar du en användare som kallas Britta Simon i RightScale. Arbeta med [Rightscale klienten supportteamet](mailto:support@rightscale.com) att lägga till användare i RightScale-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rightscale.

![Tilldela användare][200] 

**Om du vill tilldela Rightscale Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Rightscale**.

    ![Konfigurera enkel inloggning](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen RightScale på åtkomstpanelen du bör få automatiskt loggat in på ditt RightScale program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

