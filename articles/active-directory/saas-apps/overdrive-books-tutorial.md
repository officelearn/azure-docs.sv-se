---
title: 'Självstudier: Azure Active Directory-integration med högre växel för datadrivet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och högre växel för datadrivet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 3d4df5db47cb8007599ca2a09608b23f382e3fbb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420557"
---
# <a name="tutorial-azure-active-directory-integration-with-overdrive"></a>Självstudier: Azure Active Directory-integration med högre växel för datadrivet 

I den här självstudien får du lära dig hur du integrerar högre växel för datadrivet med Azure Active Directory (AD Azure).

Integrera högre växel för datadrivet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till högre växel för datadrivet 
- Du kan aktivera användarna att automatiskt få loggat in på högre växel för datadrivet (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med högre växel för datadrivet behöver du följande objekt:

- En Azure AD-prenumeration
- En högre växel för datadrivet enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till högre växel för datadrivet från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-overdrive-from-the-gallery"></a>Att lägga till högre växel för datadrivet från galleriet
Om du vill konfigurera integreringen av högre växel för datadrivet till Azure AD, som du behöver lägga till högre växel för datadrivet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till högre växel för datadrivet från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **högre växel för datadrivet**.

    ![Skapa en Azure AD-användare för testning](./media/overdrive-books-tutorial/tutorial_overdrivebooks_search.png)

1. I resultatpanelen väljer **högre växel för datadrivet**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/overdrive-books-tutorial/tutorial_overdrivebooks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med högre växel för datadrivet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i högre växel för datadrivet är till en användare i Azure AD. Med andra ord behöver en länk förhållandet mellan en Azure AD-användare och relaterade användaren i högre växel för datadrivet upprättas.

I högre växel för datadrivet tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med högre växel för datadrivet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en högre växel för datadrivet testanvändare](#creating-an-overdrive-test-user)**  – du har en motsvarighet för Britta Simon i högre växel för datadrivet som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program högre växel för datadrivet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med högre växel för datadrivet:**

1. I Azure-portalen på den **högre växel för datadrivet** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/overdrive-books-tutorial/tutorial_overdrivebooks_samlbase.png)

1. På den **högre växel för datadrivet domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/overdrive-books-tutorial/tutorial_overdrivebooks_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `http://<subdomain>.libraryreserve.com`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [högre växel för datadrivet klienten supportteamet](https://help.overdrive.com/) att hämta värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/overdrive-books-tutorial/tutorial_overdrivebooks_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/overdrive-books-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **högre växel för datadrivet** sida, som du behöver skicka de hämtade **XML-Metadata för** till [högre växel för datadrivet supportteamet](https://help.overdrive.com/). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/overdrive-books-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/overdrive-books-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/overdrive-books-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/overdrive-books-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-overdrive-test-user"></a>Skapa en högre växel för datadrivet testanvändare

Det finns ingen åtgärd-objekt som du kan konfigurera användaretablering till högre växel för datadrivet.  

När en tilldelad användare försöker logga in på högre växel för datadrivet skapas automatiskt ett högre växel för datadrivet-konto om det behövs.

>[!NOTE]
>Du kan använda alla andra högre växel för datadrivet användare konto verktyg för att skapa eller API: er som tillhandahålls av högre växel för datadrivet att etablera AAD-användarkonton.
>

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till högre växel för datadrivet.

![Tilldela användare][200] 

**Om du vill tilldela högre växel för datadrivet Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **högre växel för datadrivet**.

    ![Konfigurera enkel inloggning](./media/overdrive-books-tutorial/tutorial_overdrivebooks_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen högre växel för datadrivet i åtkomstpanelen du bör få automatiskt loggat in på ditt program högre växel för datadrivet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/overdrive-books-tutorial/tutorial_general_01.png
[2]: ./media/overdrive-books-tutorial/tutorial_general_02.png
[3]: ./media/overdrive-books-tutorial/tutorial_general_03.png
[4]: ./media/overdrive-books-tutorial/tutorial_general_04.png

[100]: ./media/overdrive-books-tutorial/tutorial_general_100.png

[200]: ./media/overdrive-books-tutorial/tutorial_general_200.png
[201]: ./media/overdrive-books-tutorial/tutorial_general_201.png
[202]: ./media/overdrive-books-tutorial/tutorial_general_202.png
[203]: ./media/overdrive-books-tutorial/tutorial_general_203.png

