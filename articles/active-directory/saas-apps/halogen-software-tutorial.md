---
title: 'Självstudier: Azure Active Directory-integrering med Halogen programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Halogen programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1ef8db71a6a9765eac4ec6ac8cae1d731e296b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205893"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Självstudier: Azure Active Directory-integrering med Halogen programvara

Lär dig hur du integrerar Halogen programvara med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Halogen programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Halogen programvara
- Du kan aktivera användarna att automatiskt få loggat in på Halogen programvara (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Halogen programvara, behöver du följande objekt:

- En Azure AD-prenumeration
- En Halogen programvara enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till Halogen programvara från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-halogen-software-from-the-gallery"></a>Lägga till Halogen programvara från galleriet

För att konfigurera integrering av Halogen programvara i Azure AD, som du behöver lägga till Halogen programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Halogen programvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **halogenatomer programvara**.

    ![Skapa en Azure AD-användare för testning](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

1. I resultatpanelen väljer **halogenatomer programvara**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Halogen programvara baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Halogen programvara till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Halogen programvara upprättas.

I Halogen programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Halogen programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Halogen programvara](#creating-a-halogen-software-test-user)**  – du har en motsvarighet för Britta Simon Halogen programvara som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Halogen programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Halogen programvara:**

1. I Azure-portalen på den **halogenatomer programvara** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

1. På den **halogenatomer programvara domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://global.hgncloud.com/<companyname>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [halogenatomer klientprogrammet supportteamet](https://support.halogensoftware.com/) att hämta dessa värden. 
 


1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster inloggning till din **halogenatomer programvara** program som administratör.

1. Klicka på den **alternativ** fliken. 
   
    ![Vad är Azure AD Connect?][12]

1. I det vänstra navigeringsfönstret klickar du på **SAML-konfiguration**. 
   
    ![Vad är Azure AD Connect?][13]

1. På den **SAML-konfiguration** utför följande steg: 

    ![Vad är Azure AD Connect?][14]

     a. Som **Unik identifierare**väljer **NameID**.

     b. Som **unika identifierare Maps till**väljer **användarnamn**.
  
     c. Om du vill ladda upp din hämtade metadatafilen, klickar du på **Bläddra** att välja filen, och sedan **Överför fil**.
 
     d. Testa konfigurationen genom att klicka på **kör Test**. 
    
    >[!NOTE]
    >Du måste vänta tills meddelandet ”*SAML-testet är klart. Stäng det här fönstret*”. Stäng webbläsarfönstret öppen. Den **aktivera SAML** kryssrutan är bara tillgänglig om testet har slutförts. 
     
     e. Välj **aktivera SAML**.
    
     f. Klicka på **Spara ändringar**. 

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/halogen-software-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/halogen-software-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/halogen-software-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typnamn som **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-halogen-software-test-user"></a>Skapa en testanvändare Halogen programvara

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon Halogen programvara.

**Utför följande steg för att skapa en användare som kallas Britta Simon Halogen programvara:**

1. Logga in på din **halogenatomer programvara** program som administratör.

1. Klicka på den **användaren Center** fliken och klicka sedan på **Create User**.
   
    ![Vad är Azure AD Connect?][300]  

1. På den **ny användare** dialogrutan utför följande steg:
   
    ![Vad är Azure AD Connect?][301]

    a. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.
    
    b. I den **efternamn** textrutan Skriv Efternamn för användaren som **Simon**. 

    c. I den **användarnamn** textrutan typ **Britta Simon**, användarnamnet som i Azure-portalen.

    d. I den **lösenord** textrutan anger du ett lösenord för Britta.
    
    e. Klicka på **Spara**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Halogen programvara.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Halogen programvara, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **halogenatomer programvara**.

    ![Konfigurera enkel inloggning](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Halogen programvara i åtkomstpanelen du bör få automatiskt loggat in på programmets Halogen programvara.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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
