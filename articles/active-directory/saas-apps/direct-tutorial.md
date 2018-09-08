---
title: 'Självstudier: Azure Active Directory-integrering med direktanslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och direct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7c2cd1f0-d14c-42f0-94a8-9b800008b285
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: jeedes
ms.openlocfilehash: 7e693a721e5556970607fafd8ff187d3b06c913e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092894"
---
# <a name="tutorial-azure-active-directory-integration-with-direct"></a>Självstudier: Azure Active Directory-integrering med direktanslutning

Lär dig hur du integrerar direkt med Azure Active Directory (AD Azure) i den här självstudien.

Integrera direkt med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till direkt
- Du kan aktivera användarna att automatiskt få loggat in på dirigera (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med direct, behöver du följande objekt:

- En Azure AD-prenumeration
- En direkt enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägger till direkt från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-direct-from-the-gallery"></a>Lägger till direkt från galleriet

Om du vill konfigurera integreringen av direkt till Azure AD, som du behöver lägga till direkt från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till direkt från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **direkt**. Välj **direkt** i resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/direct-tutorial/tutorial_direct_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med direct baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till användaren i direkt motsvarighet till en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren direkt krävs ska upprättas.

I direct, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med direct, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en direkt testanvändare](#creating-a-direct-test-user)**  – du har en motsvarighet för Britta Simon i direct som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med direkt.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med direktanslutning:**

1. I Azure-portalen på den **direkt** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/direct-tutorial/tutorial_direct_samlbase.png)

3. På den **dirigera domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/direct-tutorial/tutorial_direct_url.png)

    I den **identifierare** textrutan anger du URL: `https://direct4b.com/`

4. Kontrollera **visa avancerade URL-inställningar**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/direct-tutorial/tutorial_direct_url1.png)

     I den **inloggnings-URL** textrutan anger du URL: `https://direct4b.com/sso` 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/direct-tutorial/tutorial_direct_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/direct-tutorial/tutorial_general_400.png)

7. Att konfigurera enkel inloggning på **direkt** sida, som du behöver skicka de hämtade **XML-Metadata för** till [direct-support-teamet](https://direct4b.com/ja/support.html#inquiry).

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/direct-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/direct-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/direct-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/direct-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-direct-test-user"></a>Skapa en direkt test-användare

I det här avsnittet skapar du en användare som kallas Britta Simon i direct. Arbeta med [direct-support-teamet](https://direct4b.com/ja/support.html#inquiry) att lägga till användare i direct-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon du använder Azure enkel inloggning genom att ge åtkomst för att dirigera.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon att dirigera, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **direkt**.

    ![Konfigurera enkel inloggning](./media/direct-tutorial/tutorial_direct_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

1. Om du vill testa i **IDP-initierad läge**:

    När du klickar på den **direkt** panelen i åtkomstpanelen, som du bör få automatiskt loggat in på ditt **direkt** program.

2. Om du vill testa i **SP-initierat läge**:

    a. Klicka på den **direkt** panelen i åtkomstpanelen och du kommer att omdirigeras till inloggning programsidan.

    b. Ange din `subdomain` i textrutan som visas och tryck på '次へ (nästa), och du bör få automatiskt loggat in på ditt **direkt** program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/direct-tutorial/tutorial_general_01.png
[2]: ./media/direct-tutorial/tutorial_general_02.png
[3]: ./media/direct-tutorial/tutorial_general_03.png
[4]: ./media/direct-tutorial/tutorial_general_04.png

[100]: ./media/direct-tutorial/tutorial_general_100.png

[200]: ./media/direct-tutorial/tutorial_general_200.png
[201]: ./media/direct-tutorial/tutorial_general_201.png
[202]: ./media/direct-tutorial/tutorial_general_202.png
[203]: ./media/direct-tutorial/tutorial_general_203.png