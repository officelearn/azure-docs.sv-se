---
title: 'Självstudier: Azure Active Directory-integration med OpenAthens | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 269b216a94b1233c5f9f9a634fda3c05e46cac90
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435921"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Självstudier: Azure Active Directory-integration med OpenAthens

I den här självstudien får du lära dig hur du integrerar OpenAthens med Azure Active Directory (AD Azure).

Integrera OpenAthens med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till OpenAthens.
- Du kan aktivera användarna att logga in automatiskt till OpenAthens (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OpenAthens, behöver du följande objekt:

- En Azure AD-prenumeration
- En OpenAthens enkel inloggning aktiverat prenumeration

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [skaffa en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till OpenAthens från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-openathens-from-the-gallery"></a>Att lägga till OpenAthens från galleriet
För att konfigurera integrering av OpenAthens i Azure AD, som du behöver lägga till OpenAthens från galleriet i din lista över hanterade SaaS-appar.

**Lägga till OpenAthens från galleriet**

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Bläddra till **företagsprogram**, och gå till **alla program**.

    ![Fönstret för Enterprise-program][2]
    
1. Om du vill lägga till nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **OpenAthens**väljer **OpenAthens** i resultatrutan och välj sedan den **Lägg till** knappen.

    ![OpenAthens i resultatlistan](./media/openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OpenAthens baserat på en användare med namnet ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i OpenAthens är för användaren i Azure AD. Med andra ord måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i OpenAthens.

I OpenAthens, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med OpenAthens, måste du utföra följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on), för att ge användarna använda den här funktionen.
1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user), för att testa Azure AD enkel inloggning med Britta Simon.
1. [Skapa en testanvändare OpenAthens](#create-a-openathens-test-user), har en motsvarighet för Britta Simon i OpenAthens som är länkad till en Azure AD-representation av användaren.
1. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)att aktivera Britta Simon att använda Azure AD enkel inloggning.
1. [Testa enkel inloggning](#test-single-sign-on), för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt OpenAthens program.

**Konfigurera Azure AD enkel inloggning med OpenAthens**

1. I Azure-portalen på den **OpenAthens** application integration markerar **enkel inloggning**.

    ![Konfigurera länken för enkel inloggning][4]

1. Att aktivera enkel inloggning, i den **enkel inloggning** dialogrutan **SAML-baserad inloggning** som den **läge**.
 
    ![Enkel inloggning för dialogrutan](./media/openathens-tutorial/tutorial_openathens_samlbase.png)

1. I den **OpenAthens domän och URL: er** anger värdet `https://login.openathens.net/saml/2/metadata-sp` i den **identifierare** textrutan.

    ![OpenAthens domän och URL: er med enkel inloggning för information](./media/openathens-tutorial/tutorial_openathens_url.png)

1. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**, och spara sedan metadatafilen på datorn.

    ![Signeringscertifikat för AMSL nedladdningslänk](./media/openathens-tutorial/tutorial_openathens_certificate.png) 

1. Välj knappen **Spara**.

    ![Den enkel inloggning spara knappen](./media/openathens-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster logga du in på webbplatsen OpenAthens företag som administratör.

1. Välj **anslutningar** i listan under den **Management** fliken. 

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Välj **SAML 1.1/2.0**, och välj sedan den **konfigurera** knappen.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
1. Om du vill lägga till konfigurationen, Välj den **Bläddra** om du vill överföra metadata XML-filen som du laddade ned från Azure-portalen och välj sedan **Lägg till**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Utför följande steg under den **information** fliken.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. I **visa Namnmappningen**väljer **Använd attributet**.

    b. I den **visa namnattributet** text, ange värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. I **unika användarmappning**väljer **Använd attributet**.

    d. I den **unika användarattribut** text, ange värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. I **Status**, markera alla tre kryssrutorna.

    f. I **skapa lokala konton**väljer **automatiskt**.

    g. Välj **spara ändringar**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och komma åt den inbäddade dokumentation genom den **Configuration** avsnittet längst ned. Mer information om funktionen embedded-dokumentation finns i den [embedded-dokumentation för Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas ”Britta Simon”.

   ![Skapa en Azure AD-testanvändare][100]

**Skapa en testanvändare i Azure AD**

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/openathens-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/openathens-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/openathens-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/openathens-tutorial/create_aaduser_04.png)

    a. I den **namn** textruta, typ **BrittaSimon**.

    b. I den **användarnamn** text skriver du e-postadressen för Britta Simon.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** textrutan.

    d. Välj **Skapa**.
  
### <a name="create-an-openathens-test-user"></a>Skapa en OpenAthens testanvändare

OpenAthens stöder just-in-time-etablering och användare skapas automatiskt efter en lyckad autentisering. Du behöver inte göra någonting i det här avsnittet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst OpenAthens.

![Tilldela rollen][200] 

**Tilldela Britta Simon till OpenAthens**

1. I Azure-portalen, öppnar programmen visa, bläddrar du till vyn directory och gå till **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

1. I den **program** väljer **OpenAthens**.

    ![Länken OpenAthens i listan med program](./media/openathens-tutorial/tutorial_openathens_app.png)  

1. I menyn till vänster väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg till tilldelning** fönstret.

    ![Fönstret Lägg till tilldelning][203]

1. I den **användare och grupper** väljer **Britta Simon**.

1. Välj den **Välj** knappen i den **användare och grupper** lista.

1. Välj den **tilldela** knappen i den **Lägg till tilldelning** fönstret.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **OpenAthens** panelen i åtkomstpanelen, du bör vara automatiskt inloggad till OpenAthens programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* En lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory finns i [SaaS-appintegration för användning med Azure AD](tutorial-list.md).
* Läs mer om programåtkomst och enkel inloggning med Azure Active Directory, [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

<!--Image references-->

[1]: ./media/openathens-tutorial/tutorial_general_01.png
[2]: ./media/openathens-tutorial/tutorial_general_02.png
[3]: ./media/openathens-tutorial/tutorial_general_03.png
[4]: ./media/openathens-tutorial/tutorial_general_04.png

[100]: ./media/openathens-tutorial/tutorial_general_100.png

[200]: ./media/openathens-tutorial/tutorial_general_200.png
[201]: ./media/openathens-tutorial/tutorial_general_201.png
[202]: ./media/openathens-tutorial/tutorial_general_202.png
[203]: ./media/openathens-tutorial/tutorial_general_203.png

