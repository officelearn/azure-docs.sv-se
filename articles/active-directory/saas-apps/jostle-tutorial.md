---
title: 'Självstudier: Azure Active Directory-integrering med Jostle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jostle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9ca4ca1f-8f68-4225-81a6-1666b486d6a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 214cd2ac20207e32d862086f82f6e3c775d88721
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210449"
---
# <a name="tutorial-azure-active-directory-integration-with-jostle"></a>Självstudier: Azure Active Directory-integrering med Jostle

I den här självstudien får du lära dig hur du integrerar Jostle med Azure Active Directory (AD Azure).

Integrera Jostle med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jostle
- Du kan aktivera användarna att automatiskt få loggat in på Jostle (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jostle, behöver du följande objekt:

- En Azure AD-prenumeration
- En Jostle enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Jostle från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jostle-from-the-gallery"></a>Att lägga till Jostle från galleriet
För att konfigurera integrering av Jostle i Azure AD, som du behöver lägga till Jostle från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jostle från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]

1. Klicka på **Lägg till** överst i fönstret.

    ![add_01](./media/jostle-tutorial/add_01.png)

1. I sökrutan under **lägga till ett program** typ **Jostle**.

    ![add_02](./media/jostle-tutorial/add_02.png)

1. I resultatpanelen väljer **Jostle**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/jostle-tutorial/tutorial_jostle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Jostle baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Jostle är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Jostle upprättas.

I Jostle, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jostle, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Jostle](#creating-a-jostle-test-user)**  – du har en motsvarighet för Britta Simon i Jostle som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Jostle program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jostle:**

1. I Azure-portalen på den **Jostle** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/jostle-tutorial/tutorial_jostle_samlbase.png)

1. På den **Jostle domän och URL: er** avsnittet, utför följande steg:

    ![url_01](./media/jostle-tutorial/url_01.png)

    a. I den **inloggnings-URL** textrutan anger du: `https://login-prod.jostle.us`

    b. I den **identifierare** textrutan anger du: `https://jostle.us`

    c. Markera kryssrutan bredvid **visa avancerade URL-inställningar**

    d. I den **svars-URL** textrutan anger du: `https://login-prod.jostle.us/saml/SSO/alias/newjostle.us`

1. På den **användarattribut** avsnittet för den **användaridentifierare** fältet, anger du: `user.userprincipalname`

    ![url_02](./media/jostle-tutorial/url_02.png)

1. Klicka på **spara** överst i fönstret.

1. Gå till **SAML-signeringscertifikat** och kontrollera att den är inställd på **Active**. Klicka sedan på **XML-Metadata för** att hämta för metadatafilen.

    ![url_03](./media/jostle-tutorial/url_03.png)

1. Om du vill konfigurera enkel inloggning på Jostles sida, du behöver skicka hämtade XML-metadata till [Jostle supportteamet](mailto:support@jostle.me). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
>

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/jostle-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/jostle-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/jostle-tutorial/create_aaduser_03.png)

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/jostle-tutorial/create_aaduser_04.png)

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-jostle-test-user"></a>Skapa en Jostle testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Jostle. Om du inte vet hur du lägger till Britta Simon i Jostle, kontakta med [Jostle supportteamet](mailto:support@jostle.me) att lägga till användaren och aktivera enkel inloggning.

> [!NOTE]
> Azure Active Directory-kontoinnehavare tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jostle.

![Tilldela användare][200]

**Om du vill tilldela Britta Simon Jostle, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **Jostle**.

    ![Konfigurera enkel inloggning](./media/jostle-tutorial/tutorial_jostle_app.png)

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Jostle i åtkomstpanelen, får du automatiskt inloggningssidan i Jostle program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/jostle-tutorial/tutorial_general_01.png
[2]: ./media/jostle-tutorial/tutorial_general_02.png
[3]: ./media/jostle-tutorial/tutorial_general_03.png
[4]: ./media/jostle-tutorial/tutorial_general_04.png

[100]: ./media/jostle-tutorial/tutorial_general_100.png

[200]: ./media/jostle-tutorial/tutorial_general_200.png
[201]: ./media/jostle-tutorial/tutorial_general_201.png
[202]: ./media/jostle-tutorial/tutorial_general_202.png
[203]: ./media/jostle-tutorial/tutorial_general_203.png
