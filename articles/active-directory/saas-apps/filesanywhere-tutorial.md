---
title: 'Självstudier: Azure Active Directory-integrering med FilesAnywhere | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FilesAnywhere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: 2dab43499d5f55474117f88cfaa1cecd1a50bf3e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822050"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Självstudier: Azure Active Directory-integrering med FilesAnywhere

I den här självstudien får du lära dig hur du integrerar FilesAnywhere med Azure Active Directory (AD Azure).

Integrera FilesAnywhere med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FilesAnywhere
- Du kan aktivera användarna att automatiskt få loggat in på FilesAnywhere (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FilesAnywhere, behöver du följande objekt:

- En Azure AD-prenumeration
- En FilesAnywhere enkel inloggning aktiverad prenumeration


> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.


Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till FilesAnywhere från galleriet
1. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-filesanywhere-from-the-gallery"></a>Att lägga till FilesAnywhere från galleriet
För att konfigurera integrering av FilesAnywhere i Azure AD, som du behöver lägga till FilesAnywhere från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FilesAnywhere från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **FilesAnywhere**.

    ![Skapa en Azure AD-användare för testning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

1. I resultatpanelen väljer **FilesAnywhere**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med FilesAnywhere baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i FilesAnywhere är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i FilesAnywhere upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i FilesAnywhere.

Om du vill konfigurera och testa Azure AD enkel inloggning med FilesAnywhere, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare FilesAnywhere](#creating-a-filesanywhere-test-user)**  – du har en motsvarighet för Britta Simon i FilesAnywhere som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt FilesAnywhere program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FilesAnywhere:**

1. I hanteringsportalen för Azure på den **FilesAnywhere** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

1. På den **FilesAnywhere domän och URL: er** om du vill konfigurera programmet i **IDP-initierad läge**:

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Tänk på att värdet **215** är en **clientid** och är bara ett exempel. Du måste ersätta det med det faktiska clientid-värdet.

1. På den **FilesAnywhere domän och URL: er** om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Klicka på den **visa avancerade URL-inställningar** alternativet

    b. I den **inloggning på URL: en** textrutan anger du ett URL med hjälp av följande mönster: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Observera att detta inte är de verkliga värdena. Du måste uppdatera dessa värden med de faktiska logga på URL: en och svars-URL. Kontakta [FilesAnywhere supportteamet](mailto:support@FilesAnywhere.com) att hämta dessa värden. 

1. FilesAnywhere programvara förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    När användarna registrerar sig med FilesAnywhere de hämta värdet för **clientid** attribut från [FilesAnywhere team](mailto:support@FilesAnywhere.com). Du måste lägga till attributet ”klient-Id” med det unika värdet som tillhandahålls av FilesAnywhere. Dessa attribut som visas ovan krävs.
    > [!NOTE] 
    > Tänk på att värdet **2331** av **clientid** är bara ett exempel. Du måste ange det faktiska värdet.


1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | clientid | *"uniquevalue"* |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_general_400.png)

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

1. På den **FilesAnywhere Configuration** klickar du på **konfigurera FilesAnywhere** att öppna **konfigurera inloggning** fönster.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

1.  Om du vill hämta SSO-konfiguration slutförd för ditt program FilesAnywhere slutet, kontakta [FilesAnywhere supportteamet](mailto:support@FilesAnywhere.com) och ge dem hämtade SAML-token, registrera certifikat och enkel inloggning (SSO) URL: en.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/filesanywhere-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/filesanywhere-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/filesanywhere-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 



### <a name="creating-a-filesanywhere-test-user"></a>Skapa en FilesAnywhere testanvändare

Programmet stöder just-in-time-användaretablering, och efter autentiseringen skapas användare automatiskt i programmet. 


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst FilesAnywhere.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon FilesAnywhere, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **FilesAnywhere**.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen FilesAnywhere i åtkomstpanelen du bör få automatiskt loggat in på ditt FilesAnywhere program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
