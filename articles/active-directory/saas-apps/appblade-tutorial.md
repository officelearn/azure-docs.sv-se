---
title: 'Självstudier: Azure Active Directory-integrering med AppBlade | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AppBlade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3360d7aa-6518-4f99-88bd-b7f7258183e8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 119647c4aeabc8f4a6dd3a1933fcfafe6b6cf5c2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813329"
---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Självstudier: Azure Active Directory-integrering med AppBlade

I den här självstudien får du lära dig hur du integrerar AppBlade med Azure Active Directory (AD Azure).

Integrera AppBlade med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AppBlade
- Du kan aktivera användarna att automatiskt få loggat in på AppBlade (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med AppBlade, behöver du följande objekt:

- En Azure AD-prenumeration
- En AppBlade enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till AppBlade från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-appblade-from-the-gallery"></a>Att lägga till AppBlade från galleriet
För att konfigurera integrering av AppBlade i Azure AD, som du behöver lägga till AppBlade från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AppBlade från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

4. I sökrutan skriver **AppBlade**.

    ![Skapa en Azure AD-användare för testning](./media/appblade-tutorial/tutorial_appblade_search.png)

5. I resultatpanelen väljer **AppBlade**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/appblade-tutorial/tutorial_appblade_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med AppBlade baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i AppBlade är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i AppBlade upprättas.

I AppBlade, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med AppBlade, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AppBlade](#creating-an-appblade-test-user)**  – du har en motsvarighet för Britta Simon i AppBlade som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt AppBlade program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AppBlade:**

1. I Azure-portalen på den **AppBlade** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/appblade-tutorial/tutorial_appblade_samlbase.png)

3. På den **AppBlade domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/appblade-tutorial/tutorial_appblade_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.appblade.com/saml/<tenantid>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [AppBlade klienten supportteamet](mailto:support@appblade.com) att hämta värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/appblade-tutorial/tutorial_appblade_certificate.png) 

5. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/appblade-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **AppBlade** sida, som du behöver skicka de hämtade **XML-Metadata för** till [AppBlade supportteamet](mailto:support@appblade.com). Dessutom be dem för att konfigurera den **utfärdar-URL för enkel inloggning** som `https://appblade.com/saml`. Den här inställningen krävs för enkel inloggning ska fungera.


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/appblade-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/appblade-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/appblade-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/appblade-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-appblade-test-user"></a>Skapa en AppBlade testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i AppBlade. AppBlade stöder just-in-time-etablering, vilket är som standard aktiverat. **Kontrollera att domännamnet är konfigurerad med AppBlade för användaretablering. Etablerar fungerar efter att endast just-in-time-användaren.**

Om användaren har en e-postadress som slutar med den domän som konfigurerats av AppBlade för ditt konto, och sedan användaren ansluts automatiskt till kontot som en medlem med den behörighetsnivå som du anger, vilket är en av ”Basic” (en grundläggande användare kan bara installera program) , ”Gruppmedlem” (en användare som kan ladda upp nya versioner och hantera projekt), eller ”administratör” (fullständiga administratörsrättigheter till kontot). En skulle normalt välja Basic och lyft sedan upp användare manuellt via en administratörsinloggning (AppBlade måste konfigurera antingen en e-postbaserad administratörsinloggning i förväg eller marknadsföra en användare för kundens räkning efter inloggning).

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt AppBlade om det inte finns ännu. 

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta den [AppBlade supportteamet](mailto:support@appblade.com).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AppBlade.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon AppBlade, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **AppBlade**.

    ![Konfigurera enkel inloggning](./media/appblade-tutorial/tutorial_appblade_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.  
När du klickar på panelen AppBlade i åtkomstpanelen du bör få automatiskt loggat in på ditt AppBlade program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/appblade-tutorial/tutorial_general_01.png
[2]: ./media/appblade-tutorial/tutorial_general_02.png
[3]: ./media/appblade-tutorial/tutorial_general_03.png
[4]: ./media/appblade-tutorial/tutorial_general_04.png

[100]: ./media/appblade-tutorial/tutorial_general_100.png

[200]: ./media/appblade-tutorial/tutorial_general_200.png
[201]: ./media/appblade-tutorial/tutorial_general_201.png
[202]: ./media/appblade-tutorial/tutorial_general_202.png
[203]: ./media/appblade-tutorial/tutorial_general_203.png

