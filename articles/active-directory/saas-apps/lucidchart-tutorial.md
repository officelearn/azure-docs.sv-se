---
title: 'Självstudier: Azure Active Directory-integration med Lucidchart | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: cdc883adb321d4efffd1580bdb730f998f9b5b47
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051905"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Självstudier: Azure Active Directory-integration med Lucidchart

I den här självstudien får du lära dig hur du integrerar Lucidchart med Azure Active Directory (AD Azure).

Integrera Lucidchart med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Lucidchart
- Du kan aktivera användarna att automatiskt få loggat in på Lucidchart (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Lucidchart, behöver du följande objekt:

- En Azure AD-prenumeration
- En Lucidchart enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Lucidchart från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-lucidchart-from-the-gallery"></a>Att lägga till Lucidchart från galleriet
För att konfigurera integrering av Lucidchart i Azure AD, som du behöver lägga till Lucidchart från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Lucidchart från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Lucidchart**.

    ![Skapa en Azure AD-användare för testning](./media/lucidchart-tutorial/tutorial_lucidchart_search.png)

5. I resultatpanelen väljer **Lucidchart**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/lucidchart-tutorial/tutorial_lucidchart_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Lucidchart baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Lucidchart är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Lucidchart upprättas.

I Lucidchart, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Lucidchart, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Lucidchart](#creating-a-lucidchart-test-user)**  – du har en motsvarighet för Britta Simon i Lucidchart som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Lucidchart program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Lucidchart:**

1. I Azure-portalen på den **Lucidchart** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/lucidchart-tutorial/tutorial_lucidchart_samlbase.png)

3. På den **Lucidchart domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/lucidchart-tutorial/tutorial_lucidchart_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://chart2.office.lucidchart.com/saml/sso/azure`

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/lucidchart-tutorial/tutorial_lucidchart_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/lucidchart-tutorial/tutorial_general_400.png)

6. Logga in på webbplatsen Lucidchart företag som en administratör i ett annat webbläsarfönster.

7. Klicka på menyn längst upp **Team**.
   
    ![Team](./media/lucidchart-tutorial/ic791190.png "Team")

8. Klicka på **program \> hantera SAML**.
   
    ![Hantera SAML](./media/lucidchart-tutorial/ic791191.png "hantera SAML")

9. På den **SAML autentiseringsinställningar** dialogrutan utför följande steg:
   
    a. Välj **aktivera SAML-autentisering**, och klicka sedan på **valfritt**.

    ![SAML-autentiseringsinställningar](./media/lucidchart-tutorial/ic791192.png "SAML-autentiseringsinställningar")
 
    b. I den **domän** textrutan Skriv din domän och klicka på **ändra certifikat**.

    ![Ändra certifikat](./media/lucidchart-tutorial/ic791193.png "ändra certifikat")
 
    c. Öppna din hämtade metadatafilen, kopiera innehållet och klistra in den i den **överföra Metadata** textrutan.

    ![Ladda upp Metadata](./media/lucidchart-tutorial/ic791194.png "överföra Metadata")
 
    d. Välj **automatiskt lägga till nya användare till teamet**, och klicka sedan på **spara ändringar**.

    ![Spara ändringar](./media/lucidchart-tutorial/ic791195.png "spara ändringar")

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/lucidchart-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/lucidchart-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/lucidchart-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/lucidchart-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-lucidchart-test-user"></a>Skapa en Lucidchart testanvändare

Det finns inga uppgift att konfigurera användaretablering för Lucidchart.  När en tilldelad användare försöker logga in med hjälp av åtkomstpanelen Lucidchart kontrollerar Lucidchart om användaren finns.  

Om det finns inget konto ännu, skapas den automatiskt av Lucidchart.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Lucidchart.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Lucidchart, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Lucidchart**.

    ![Konfigurera enkel inloggning](./media/lucidchart-tutorial/tutorial_lucidchart_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Lucidchart i åtkomstpanelen du bör få automatiskt loggat in på ditt Lucidchart program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lucidchart-tutorial/tutorial_general_01.png
[2]: ./media/lucidchart-tutorial/tutorial_general_02.png
[3]: ./media/lucidchart-tutorial/tutorial_general_03.png
[4]: ./media/lucidchart-tutorial/tutorial_general_04.png

[100]: ./media/lucidchart-tutorial/tutorial_general_100.png

[200]: ./media/lucidchart-tutorial/tutorial_general_200.png
[201]: ./media/lucidchart-tutorial/tutorial_general_201.png
[202]: ./media/lucidchart-tutorial/tutorial_general_202.png
[203]: ./media/lucidchart-tutorial/tutorial_general_203.png

