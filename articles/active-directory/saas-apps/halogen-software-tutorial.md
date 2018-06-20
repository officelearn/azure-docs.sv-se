---
title: 'Självstudier: Azure Active Directory-integrering med Halogen programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Halogen programvaran och Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 7699eb4074d85768fa18f4739137ea8ad2b80ced
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215434"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Självstudier: Azure Active Directory-integrering med Halogen programvara

I kursen får lära du att integrera Halogen programvara med Azure Active Directory (AD Azure).

Integrera Halogen programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Halogen programvara
- Du kan aktivera användarna att automatiskt hämta loggat in på Halogen programvara (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Halogen programvara, behöver du följande:

- En Azure AD-prenumeration
- En Halogen programvara enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till Halogen programvara från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-halogen-software-from-the-gallery"></a>Lägga till Halogen programvara från galleriet

Du måste lägga till Halogen programvara från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Halogen programvara i Azure AD.

**Utför följande steg för att lägga till Halogen programvara från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Halogen programvara**.

    ![Skapa en testanvändare i Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

5. Välj i resultatpanelen **Halogen programvara**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Halogen programvara baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Halogen programvara till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Halogen programvara upprättas.

I Halogen programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Halogen programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Halogen programvara](#creating-a-halogen-software-test-user)**  – du har en motsvarighet för Britta Simon Halogen programvara som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Halogen programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Halogen programvara:**

1. I Azure-portalen på den **Halogen programvara** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

3. På den **Halogen programvara domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://global.hgncloud.com/<companyname>`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Halogen Programvaruklienten supportteamet](https://support.halogensoftware.com/) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster inloggning till din **Halogen programvara** program som administratör.

7. Klicka på den **alternativ** fliken. 
   
    ![Vad är Azure AD Connect?][12]

8. I det vänstra navigeringsfönstret klickar du på **SAML-konfiguration**. 
   
    ![Vad är Azure AD Connect?][13]

9. På den **SAML-konfiguration** utför följande steg: 

    ![Vad är Azure AD Connect?][14]

     a. Som **Unik identifierare**väljer **NameID**.

     b. Som **unika identifierare Maps till**väljer **användarnamn**.
  
     c. Om du vill överföra din hämtade metadatafil klickar du på **Bläddra** att välja filen och sedan **överför filen**.
 
     d. Testa konfigurationen genom att klicka på **kör testa**. 
    
    >[!NOTE]
    >Du måste vänta tills meddelandet ”*SAML-testet har slutförts. Stäng det här fönstret*”. Stäng öppna webbläsarfönstret. Den **aktivera SAML** kryssrutan är endast aktiverad om testet har slutförts. 
     
     e. Välj **aktivera SAML**.
    
     f. Klicka på **spara ändringar**. 

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/halogen-software-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/halogen-software-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/halogen-software-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typnamn som **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-halogen-software-test-user"></a>Skapa en testanvändare Halogen programvara

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon Halogen programvaran.

**Utför följande steg för att skapa en användare som kallas Britta Simon Halogen programvaran:**

1. Logga in på ditt **Halogen programvara** program som administratör.

2. Klicka på den **användaren Center** fliken och klicka sedan på **skapa användare**.
   
    ![Vad är Azure AD Connect?][300]  

3. På den **ny användare** dialogrutan utför följande steg:
   
    ![Vad är Azure AD Connect?][301]

    a. I den **Förnamn** textruta första typnamnet för användaren som **Britta**.
    
    b. I den **efternamn** textruta Skriv Efternamn för användaren som **Simon**. 

    c. I den **användarnamn** textruta typen **Britta Simon**, användarnamnet som Azure-portalen.

    d. I den **lösenord** textruta, ange ett lösenord för Britta.
    
    e. Klicka på **Spara**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Halogen programvara.

![Tilldela användare][200] 

**Om du vill tilldela Halogen programvara Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Halogen programvara**.

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Halogen programvara på åtkomstpanelen du bör få automatiskt loggat in på Halogen programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/halogen-software-tutorial/tutorial_halogen_301.png
