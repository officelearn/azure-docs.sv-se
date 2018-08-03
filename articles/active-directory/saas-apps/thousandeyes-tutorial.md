---
title: 'Självstudier: Azure Active Directory-integration med ThousandEyes | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b6af7a3322b1a01c1d822df78d827121c19e21e1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444410"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Självstudier: Azure Active Directory-integration med ThousandEyes

I den här självstudien får du lära dig hur du integrerar ThousandEyes med Azure Active Directory (AD Azure).

Integrera ThousandEyes med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ThousandEyes
- Du kan aktivera användarna att automatiskt få loggat in på ThousandEyes (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ThousandEyes, behöver du följande objekt:

- En Azure AD-prenumeration
- En ThousandEyes enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här: [– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ThousandEyes från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-thousandeyes-from-the-gallery"></a>Att lägga till ThousandEyes från galleriet
För att konfigurera integrering av ThousandEyes i Azure AD, som du behöver lägga till ThousandEyes från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ThousandEyes från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **ThousandEyes**.

    ![Skapa en Azure AD-användare för testning](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. I resultatpanelen väljer **ThousandEyes**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ThousandEyes baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ThousandEyes är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ThousandEyes upprättas.

I ThousandEyes, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ThousandEyes, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare ThousandEyes](#creating-a-thousandeyes-test-user)**  – du har en motsvarighet för Britta Simon i ThousandEyes som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ThousandEyes program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ThousandEyes:**

1. I Azure-portalen på den **ThousandEyes** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. På den **ThousandEyes domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://app.thousandeyes.com/login/sso`

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. På den **ThousandEyes Configuration** klickar du på **konfigurera ThousandEyes** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. I ett annat webbläsarfönster, loggar du in på ditt **ThousandEyes** företagets plats som administratör.

1. Klicka på menyn längst upp **inställningar**.

    ![Inställningar för](./media/thousandeyes-tutorial/ic790066.png "inställningar")

1. Klicka på **konto**

    ![Kontot](./media/thousandeyes-tutorial/ic790067.png "konto")

1. Klicka på den **säkerhet och autentisering** fliken.

    ![Säkerhet och autentisering](./media/thousandeyes-tutorial/ic790068.png "säkerhet och autentisering")

1. I den **installationsprogrammet Single Sign-On** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/ic790069.png "Konfigurera enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. I **inloggnings-URL för sidan** textrutan klistra in **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. I **sid-URL för utloggning** textrutan klistra in **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    d. **Providerutgivare** textrutan klistra in **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    e. I **verifieringscertifikatet**, klickar du på **Välj fil**, och sedan ladda upp det certifikat som du har hämtat från Azure-portalen.

    f. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-thousandeyes-test-user"></a>Skapa en ThousandEyes testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ThousandEyes. ThousandEyes stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](thousandeyes-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt kan du utföra följande steg:**

1. Logga in på webbplatsen ThousandEyes företag som administratör.

1. Klicka på **inställningar**.

    ![Inställningar för](./media/thousandeyes-tutorial/IC790066.png "inställningar")

1. Klicka på **konto**.

    ![Kontot](./media/thousandeyes-tutorial/IC790067.png "konto")

1. Klicka på den **konton och användare** fliken.

    ![Konton och användare](./media/thousandeyes-tutorial/IC790073.png "konton och användare")

1. I den **lägga till användare och konton** avsnittet, utför följande steg:

    ![Lägga till användarkonton](./media/thousandeyes-tutorial/IC790074.png "lägga till användarkonton")

    a. I **namn** textrutan skriver du namnet på användaren som **Britta Simon**.

    b. I **e-post** textrutan typ e-postmeddelandet av användare som **brittasimon@contoso.com**.

    b. Klicka på **lägga till nya användare till kontot**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavare får ett e-postmeddelande, inklusive en länk för att bekräfta och aktivera kontot.

> [!NOTE]
> Du kan använda alla andra ThousandEyes användare konto verktyg för att skapa eller API: er som tillhandahålls av ThousandEyes för att etablera Azure Active Directory användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ThousandEyes.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon ThousandEyes, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **ThousandEyes**.

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ThousandEyes i åtkomstpanelen du bör få automatiskt loggat in på ditt ThousandEyes program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
