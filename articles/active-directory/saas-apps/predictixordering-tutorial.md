---
title: 'Självstudier: Azure Active Directory-integrering med Predictix ordning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Predictix ordning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 7dadb3f8680acee0c67e03ad719826f93c8486ae
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809046"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Självstudier: Azure Active Directory-integrering med Predictix ordning

I den här självstudien får du lära dig hur du integrerar Predictix ordning med Azure Active Directory (AD Azure).

Integrera Predictix ordning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Predictix ordning.
- Du kan aktivera användarna att automatiskt få loggat in på Predictix ordning (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Predictix ordning, behöver du följande objekt:

- En Azure AD-prenumeration
- En Predictix ordning enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Predictix ordning från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-predictix-ordering-from-the-gallery"></a>Att lägga till Predictix ordning från galleriet
För att konfigurera integrering av Predictix ordning i Azure AD, som du behöver lägga till Predictix ordning från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Predictix ordning från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Predictix ordning**väljer **Predictix ordning** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Predictix ordning i resultatlistan](./media/predictixordering-tutorial/tutorial_predictixordering_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Predictix ordning baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Predictix ordning är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Predictix ordning upprättas.

I Predictix ordning, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Predictix ordning, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Predictix ordning](#create-a-predictix-ordering-test-user)**  – du har en motsvarighet för Britta Simon i Predictix ordning som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Predictix ordning.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Predictix ordning:**

1. I Azure-portalen på den **Predictix ordning** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/predictixordering-tutorial/tutorial_predictixordering_samlbase.png)

1. På den **Predictix ordning domän och URL: er** avsnittet, utför följande steg:

    ![Predictix ordning domän och URL: er med enkel inloggning för information](./media/predictixordering-tutorial/tutorial_predictixordering_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: 
    
    | |
    |--|
    | `https://<companyname-pricing>.dev.ordering.predictix.com` |
    | `https://<companyname-pricing>.ordering.predictix.com` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Predictix ordning klienten supportteamet](https://www.predix.io/support/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/predictixordering-tutorial/tutorial_predictixordering_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/predictixordering-tutorial/tutorial_general_400.png)

1. På den **Predictix ordning Configuration** klickar du på **konfigurera Predictix ordning** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Predictix ordning konfiguration](./media/predictixordering-tutorial/tutorial_predictixordering_configure.png) 

1. Att konfigurera enkel inloggning på **Predictix ordning** sida, som du behöver skicka de hämtade **certifikat (Base64)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Predictix ordning supportteamet](https://www.predix.io/support/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/predictixordering-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/predictixordering-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/predictixordering-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/predictixordering-tutorial/create_aaduser_04.png)

   a. I den **namn** skriver **BrittaSimon**.

   b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

   c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

   d. Klicka på **Skapa**.
 
### <a name="create-a-predictix-ordering-test-user"></a>Skapa en testanvändare Predictix ordning

I det här avsnittet skapar du en användare som kallas Britta Simon i Predictix ordning. Arbeta med [Predictix ordning supportteamet](https://www.predix.io/support/) att lägga till användare i Predictix ordning-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Predictix ordning.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Predictix ordning, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Predictix ordning**.

    ![Länken Predictix ordning i listan med program](./media/predictixordering-tutorial/tutorial_predictixordering_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Predictix ordning i åtkomstpanelen du bör få automatiskt loggat in på programmets Predictix ordning.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/predictixordering-tutorial/tutorial_general_01.png
[2]: ./media/predictixordering-tutorial/tutorial_general_02.png
[3]: ./media/predictixordering-tutorial/tutorial_general_03.png
[4]: ./media/predictixordering-tutorial/tutorial_general_04.png

[100]: ./media/predictixordering-tutorial/tutorial_general_100.png

[200]: ./media/predictixordering-tutorial/tutorial_general_200.png
[201]: ./media/predictixordering-tutorial/tutorial_general_201.png
[202]: ./media/predictixordering-tutorial/tutorial_general_202.png
[203]: ./media/predictixordering-tutorial/tutorial_general_203.png

