---
title: 'Självstudier: Azure Active Directory-integration med Fuze | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fuze.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9780b4bf-1fd1-48c1-9ceb-f750225ae08a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 6a3fa8db14f2b155a471fd42d0d20ccf543fdbba
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115763"
---
# <a name="tutorial-azure-active-directory-integration-with-fuze"></a>Självstudier: Azure Active Directory-integration med Fuze

I den här självstudien får du lära dig hur du integrerar Fuze med Azure Active Directory (AD Azure).

Integrera Fuze med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Fuze
- Du kan aktivera användarna att automatiskt få loggat in på Fuze (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Fuze, behöver du följande objekt:

- En Azure AD-prenumeration
- En Fuze enkel inloggning aktiverad prenumeration


> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Fuze från galleriet
1. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-fuze-from-the-gallery"></a>Att lägga till Fuze från galleriet
För att konfigurera integrering av Fuze i Azure AD, som du behöver lägga till Fuze från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Fuze från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Fuze**.

    ![Skapa en Azure AD-användare för testning](./media/fuze-tutorial/tutorial_fuze_000.png)

1. I resultatpanelen väljer **Fuze**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/fuze-tutorial/tutorial_fuze_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Fuze baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Fuze är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Fuze upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Fuze.

Om du vill konfigurera och testa Azure AD enkel inloggning med Fuze, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Fuze](#creating-a-fuze-test-user)**  – du har en motsvarighet för Britta Simon i Fuze som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Fuze program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Fuze:**

1. I hanteringsportalen för Azure på den **Fuze** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/fuze-tutorial/tutorial_fuze_01.png)

1. På den **Fuze domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/fuze-tutorial/tutorial_fuze_020.png)
    
    I den **inloggnings-URL** textrutan typ i inloggnings-URL: en som: `https://www.thinkingphones.com/jetspeed/portal/`

1.  Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/fuze-tutorial/tutorial_general_400.png)

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara xml-filen på datorn.

    ![Konfigurera enkel inloggning](./media/fuze-tutorial/tutorial_fuze_05.png) 

1. Att konfigurera enkel inloggning på **Fuze** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Fuze supportteamet](https://www.fuze.com/support). De kommer har konfigurerat för att få SAML SSO-anslutningen korrekt inställda på båda sidorna.


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/fuze-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/fuze-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/fuze-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/fuze-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 


### <a name="creating-a-fuze-test-user"></a>Skapa en Fuze testanvändare

Fuze program stöder fullständig precis i tid användaren etablera, så att användare skapas automatiskt när de loggar in. För andra information kontakta Fuze [stöder](https://www.fuze.com/support).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Fuze.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Fuze, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Fuze**.

    ![Konfigurera enkel inloggning](./media/fuze-tutorial/tutorial_fuze_50.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Fuze i åtkomstpanelen du bör få automatiskt loggat in på ditt Fuze program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fuze-tutorial/tutorial_general_01.png
[2]: ./media/fuze-tutorial/tutorial_general_02.png
[3]: ./media/fuze-tutorial/tutorial_general_03.png
[4]: ./media/fuze-tutorial/tutorial_general_04.png

[100]: ./media/fuze-tutorial/tutorial_general_100.png

[200]: ./media/fuze-tutorial/tutorial_general_200.png
[201]: ./media/fuze-tutorial/tutorial_general_201.png
[202]: ./media/fuze-tutorial/tutorial_general_202.png
[203]: ./media/fuze-tutorial/tutorial_general_203.png