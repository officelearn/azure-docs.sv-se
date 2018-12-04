---
title: 'Självstudier: Azure Active Directory-integration med Mobile Xpense | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mobile Xpense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e649fc4e-3e15-4948-b977-00bfe9f7db13
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jeedes
ms.openlocfilehash: d51ced3f264a486288c951def7c23a0d95609ac0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850694"
---
# <a name="tutorial-azure-active-directory-integration-with-mobile-xpense"></a>Självstudier: Azure Active Directory-integration med Mobile Xpense

I den här självstudien får du lära dig hur du integrerar Mobile Xpense med Azure Active Directory (AD Azure).

Integrera Mobile Xpense med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Mobile Xpense.
- Du kan aktivera användarna att automatiskt få loggat in på Mobile Xpense (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mobile Xpense, behöver du följande objekt:

- En Azure AD-prenumeration
- En Mobile Xpense enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Mobile Xpense från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mobile-xpense-from-the-gallery"></a>Att lägga till Mobile Xpense från galleriet
För att konfigurera integrering av Mobile Xpense i Azure AD, som du behöver lägga till Mobile Xpense från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mobile Xpense från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Mobile Xpense**väljer **Mobile Xpense** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Mobile Xpense i resultatlistan](./media/mobilexpense-tutorial/tutorial_mobilexpense_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mobile Xpense baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Mobile Xpense är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Mobile Xpense upprättas.

I Mobile Xpense tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mobile Xpense, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Mobile Xpense](#create-a-mobile-xpense-test-user)**  – du har en motsvarighet för Britta Simon i Mobile Xpense som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för mobila Xpense.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Mobile Xpense:**

1. I Azure-portalen på den **Mobile Xpense** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/mobilexpense-tutorial/tutorial_mobilexpense_samlbase.png)

1. På den **Mobile Xpense domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Mobile Xpense domän och URL: er med enkel inloggning för information](./media/mobilexpense-tutorial/tutorial_mobilexpense_url11.png)

    a. I den **identifierare** textrutan anger du ett URL: `https://mobilexpense.com/ServiceProvider`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<sub-domain>.mobilexpense.com/NET/SSO/SAML20/SAML/AssertionConsumerService.aspx`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Mobile Xpense domän och URL: er med enkel inloggning för information](./media/mobilexpense-tutorial/tutorial_mobilexpense_url22.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<sub-domain>.mobilexpense.com/<customername>`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL och inloggnings-URL. Kontakta [Mobile Xpense klienten supportteamet](https://www.mobilexpense.net/contact) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/mobilexpense-tutorial/tutorial_mobilexpense_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/mobilexpense-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Mobile Xpense** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Mobile Xpense supportteamet](https://www.mobilexpense.net/contact). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/mobilexpense-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/mobilexpense-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/mobilexpense-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/mobilexpense-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-mobile-xpense-test-user"></a>Skapa en Mobile Xpense testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i MobileXpense. fungerar med [MobileXpense supportteamet](https://www.mobilexpense.net/contact) att lägga till användare i MobileXpense-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mobile Xpense.

![Tilldela rollen][200] 

**Om du vill tilldela Mobile Xpense Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Mobile Xpense**.

    ![Länken Mobile Xpense i listan med program](./media/mobilexpense-tutorial/tutorial_mobilexpense_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mobile Xpense i åtkomstpanelen du bör få automatiskt loggat in på ditt mobila Xpense program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mobilexpense-tutorial/tutorial_general_01.png
[2]: ./media/mobilexpense-tutorial/tutorial_general_02.png
[3]: ./media/mobilexpense-tutorial/tutorial_general_03.png
[4]: ./media/mobilexpense-tutorial/tutorial_general_04.png

[100]: ./media/mobilexpense-tutorial/tutorial_general_100.png

[200]: ./media/mobilexpense-tutorial/tutorial_general_200.png
[201]: ./media/mobilexpense-tutorial/tutorial_general_201.png
[202]: ./media/mobilexpense-tutorial/tutorial_general_202.png
[203]: ./media/mobilexpense-tutorial/tutorial_general_203.png

