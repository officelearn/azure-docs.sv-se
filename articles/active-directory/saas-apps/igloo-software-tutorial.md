---
title: 'Självstudier: Azure Active Directory-integrering med Igloo programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Igloo programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: d49a08c6f57f5248f17539cd9d0467d132f7a63d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447415"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Självstudier: Azure Active Directory-integrering med Igloo programvara

I den här självstudien får du lära dig hur du integrerar Igloo programvara med Azure Active Directory (AD Azure).

Integrera Igloo programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Igloo programvara
- Du kan aktivera användarna att automatiskt få loggat in på Igloo programvara (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Igloo programvara, behöver du följande objekt:

- En Azure AD-prenumeration
- En Igloo programvara enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till Igloo programvara från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-igloo-software-from-the-gallery"></a>Lägga till Igloo programvara från galleriet
För att konfigurera integrering av Igloo programvara i Azure AD, som du behöver lägga till Igloo programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Igloo programvara från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Igloo programvara**.

    ![Skapa en Azure AD-användare för testning](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

1. I resultatpanelen väljer **Igloo programvara**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Igloo programvara baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Igloo programvara till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Igloo programvara upprättas.

I Igloo programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Igloo programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Igloo programvara](#creating-an-igloo-software-test-user)**  – du har en motsvarighet för Britta Simon Igloo programvara som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Igloo programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Igloo programvara:**

1. I Azure-portalen på den **Igloo programvara** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

1. På den **Igloo programvara domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.igloocommmunities.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.igloocommmunities.com/saml.digest`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [Igloo klientprogrammet supportteamet](https://www.igloosoftware.com/services/support) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/igloo-software-tutorial/tutorial_general_400.png)
    
1. På den **Igloo programvarukonfiguration** klickar du på **konfigurera Igloo programvara** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen för företagets Igloo programvara som en administratör.

1. Gå till den **Kontrollpanelen**.
   
     ![Kontrollpanelen](./media/igloo-software-tutorial/ic799949.png "Kontrollpanelen")

1. I den **medlemskap** fliken **logga i inställningar**.
   
    ![Inloggningsinställningar](./media/igloo-software-tutorial/ic783968.png "inloggningsinställningar")

1. I avsnittet SAML-konfiguration klickar du på **konfigurera SAML-autentisering**.
   
    ![SAML-konfiguration](./media/igloo-software-tutorial/ic783969.png "SAML-konfiguration")
   
1. I den **Allmän konfiguration** avsnittet, utför följande steg:
   
    ![Allmän konfiguration](./media/igloo-software-tutorial/ic783970.png "Allmän konfiguration")

    a. I den **anslutningsnamn** textrutan skriver du ett eget namn för din konfiguration.
   
    b. I den **inloggnings-URL för IDP: N** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    c. I den **utloggnings-URL för IDP: N** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.
    
    d. Välj **utloggning svar och typ av begäran HTTP** som **POST**.
   
    e. Öppna din **Base64-** kodade certifikatet i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **offentligt certifikat** textrutan.
    
1. I den **svar och Autentiseringskonfiguration**, utför följande steg:
    
    ![Svar och Autentiseringskonfiguration](./media/igloo-software-tutorial/IC783971.png "svar och konfiguration av autentisering")
  
      a. Som **identitetsprovidern**väljer **Microsoft ADFS**.
      
      b. Som **typ av identifierare**väljer **e-postadress**. 

      c. I den **e-attributet** textrutan typ **e-postadress**.

      d. I den **förnamn attributet** textrutan typ **givenname**.

      e. I den **senaste namnattributet** textrutan typ **efternamn**.

1. Utför följande steg för att slutföra konfigurationen:
    
    ![Skapa användare på inloggningen](./media/igloo-software-tutorial/IC783972.png "skapa användare på Logga in") 

     a. Som **skapa användare på inloggningen**väljer **skapa en ny användare på din plats när de loggar in**.

     b. Som **inloggningsinställningar**väljer **Använd SAML-knappen ”Logga in” skärmen**.

     c. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/igloo-software-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/igloo-software-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/igloo-software-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-igloo-software-test-user"></a>Skapa en testanvändare Igloo programvara

Det finns inga uppgift att konfigurera användaretablering till Igloo programvara.  

När en tilldelad användare försöker logga in på Igloo programvara med hjälp av åtkomstpanelen, kontrollerar Igloo programvara om användaren finns.  Om det finns inget konto ännu, skapas den automatiskt med Igloo program.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Igloo programvara.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Igloo programvara, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Igloo programvara**.

    ![Konfigurera enkel inloggning](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Igloo programvara i åtkomstpanelen du bör få automatiskt loggat in på programmets Igloo programvara.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png

