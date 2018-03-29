---
title: 'Självstudier: Azure Active Directory-integrering med OpsGenie | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: b0cd54c7750e85eb7b4e1ba6be309c585d5a7b4e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Självstudier: Azure Active Directory-integrering med OpsGenie

I kursen får lära du att integrera OpsGenie med Azure Active Directory (AD Azure).

Integrera OpsGenie med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till OpsGenie
- Du kan aktivera användarna att automatiskt hämta loggat in på OpsGenie (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med OpsGenie, behöver du följande:

- En Azure AD-prenumeration
- En OpsGenie enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till OpsGenie från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-opsgenie-from-the-gallery"></a>Att lägga till OpsGenie från galleriet
Du måste lägga till OpsGenie från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av OpsGenie i Azure AD.

**Utför följande steg för att lägga till OpsGenie från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **OpsGenie**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_search.png)

5. Välj i resultatpanelen **OpsGenie**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med OpsGenie baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i OpsGenie motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i OpsGenie upprättas.

I OpsGenie, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med OpsGenie, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare OpsGenie](#creating-a-opsgenie-test-user)**  – du har en motsvarighet för Britta Simon i OpsGenie som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt OpsGenie program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med OpsGenie:**

1. I Azure-portalen på den **OpsGenie** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

3. På den **OpsGenie domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_url.png)

    I den **inloggnings-URL** textruta anger du URL: `https://app.opsgenie.com/auth/login`

4. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_general_400.png)

5. På den **OpsGenie Configuration** klickar du på **konfigurera OpsGenie** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML inloggning tjänst-URL för enkel** från avsnittet Snabbreferens.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_configure.png)

6. Att generera den **URL för tjänstmetadata**, utför följande steg:

    a. Klicka på **App registreringar**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_appregistrations.png)
   
    b. Klicka på **slutpunkter** att öppna **slutpunkter** dialogrutan.  
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_endpointicon.png)

    c. Klicka på kopieringsknappen för att kopiera **FEDERATION METADATADOKUMENTET** url och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_endpoint.png)
     
    d. Gå till egenskapssidan för **OpsGenie** och kopiera den **program-Id** med **kopiera** knappen och klistra in den i anteckningar.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_appid.png)

    e. Generera den **URL för tjänstmetadata** med hjälp av följande mönster: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

7. Öppna en annan instans för webbläsaren och sedan logga in på OpsGenie som administratör.

8. Klicka på **inställningar**, och klicka sedan på den **enkel inloggning** fliken.
   
    ![OpsGenie Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png)

9. Välj för att aktivera enkel inloggning **aktiverad**.
   
    ![OpsGenie Settings](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 

10. I den **Provider** klickar du på den **Azure Active Directory** fliken.
   
    ![OpsGenie Settings](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 

11. På sidan för Azure Active Directory-dialogrutan utför du följande steg:
   
    ![OpsGenie Settings](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. I den **SAML 2.0 Endpoint** textruta klistra in **inloggning på tjänst-URL för enkel**värde som du har kopierat från Azure-portalen.
    
    b. I den **Url för tjänstmetadata:** textruta klistra in **URL för tjänstmetadata**värde som du har kopierat från Azure-portalen.
    
    c. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-opsgenie-test-user"></a>Skapa en testanvändare OpsGenie

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i OpsGenie. 

1. Logga in på din OpsGenie klient som en administratör i ett webbläsarfönster.

2. Gå till listan genom att klicka på **användaren** i den vänstra panelen.
   
   ![OpsGenie Settings](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Klicka på **lägga till användare**.

4. På den **Lägg till användare** dialogrutan, utför följande steg:
   
   ![OpsGenie Settings](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. I den **e-post** textruta e-postadressen sorts BrittaSimon åtgärdas i Azure Active Directory.
   
   b. I den **fullständiga namn** textruta typen **Britta Simon**.
   
   c. Klicka på **Spara**. 

>[!NOTE]
>Britta får ett e-postmeddelande med instruktioner för att konfigurera sin profil.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OpsGenie.

![Tilldela användare][200] 

**Om du vill tilldela OpsGenie Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **OpsGenie**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen OpsGenie på åtkomstpanelen du bör få automatiskt loggat in på ditt OpsGenie program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png

