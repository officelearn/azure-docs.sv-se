---
title: 'Självstudier: Azure Active Directory-integrering med Menlo säkerhet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Menlo säkerhet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9e63fe6b-0ad0-405d-9e41-6a1a40a41df8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: jeedes
ms.openlocfilehash: fb4a82d1ae34c81df535e606a39eaf091f7df0da
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158068"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>Självstudier: Azure Active Directory-integrering med Menlo säkerhet

I den här självstudien får du lära dig hur du integrerar Menlo säkerhet med Azure Active Directory (AD Azure).

Integrera Menlo säkerhet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Menlo säkerhet
- Du kan aktivera användarna att automatiskt få loggat in på Menlo säkerhet (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i. [Vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Menlo säkerhet, behöver du följande objekt:

- En Azure AD-prenumeration
- En Menlo Security enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Menlo säkerhet från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-menlo-security-from-the-gallery"></a>Att lägga till Menlo säkerhet från galleriet
För att konfigurera integrering av Menlo säkerhet i Azure AD, som du behöver lägga till Menlo säkerhet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Menlo säkerhet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Menlo Security**.

    ![Skapa en Azure AD-användare för testning](./media/menlosecurity-tutorial/tutorial_menlosecurity_search.png)

1. I resultatpanelen väljer **Menlo Security**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/menlosecurity-tutorial/tutorial_menlosecurity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Menlo säkerhet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Menlo säkerhet är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Menlo Security upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Menlo Security.

Om du vill konfigurera och testa Azure AD enkel inloggning med Menlo säkerhet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Menlo Security](#creating-a-menlo-security-test-user)**  – du har en motsvarighet för Britta Simon i Menlo Security som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Menlo säkerhet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Menlo säkerhet:**

1. I Azure-portalen på den **Menlo Security** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/tutorial_menlosecurity_samlbase.png)

1. På den **Menlo säkerhetsdomän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/tutorial_menlosecurity_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.menlosecurity.com/account/login`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.menlosecurity.com/safeview-auth-server/saml/metadata`

    > [!NOTE] 
    > De här värdena är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Menlo Säkerhetsklient supportteamet](https://www.menlosecurity.com/menlo-contact) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/tutorial_menlosecurity_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/tutorial_general_400.png)

1. På den **Menlo säkerhetskonfiguration** klickar du på **konfigurera säkerheten för Menlo** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/tutorial_menlosecurity_configure.png) 

1. Att konfigurera enkel inloggning på **Menlo Security** sida, logga in på den **Menlo Security** webbplats som administratör.

1. Under **inställningar** gå till **autentisering** och utföra följande åtgärder:
    
    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/menlo_user_setup.png)

    a. Markera kryssrutan **aktivera användarautentisering med hjälp av SAML**.

    b. Välj **Tillåt extern åtkomst** till **Ja**.

    c. Under **SAML-providern**väljer **Azure Active Directory**.

    d. **SAML 2.0 slutpunkt** : Klistra in den **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    e. **Tjänsten identifierare (utfärdare)** : Klistra in den **SAML entitets-ID** som du har kopierat från Azure-portalen.

    f. **X.509-certifikat** : Öppna den **certifikat (Base64)** laddas ned från Azure-portalen i anteckningar och klistra in den i den här rutan.

    g. Spara inställningarna genom att klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/menlosecurity-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/menlosecurity-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/menlosecurity-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/menlosecurity-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-menlo-security-test-user"></a>Skapa en testanvändare Menlo säkerhet
 
I det här avsnittet skapar du en användare som kallas Britta Simon i Menlo Security. Arbeta med [Menlo Säkerhetsklient supportteamet](https://www.menlosecurity.com/menlo-contact) att lägga till användare i Menlo Security-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Menlo Security.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Menlo säkerhet, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Menlo Security**.

    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/tutorial_menlosecurity_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du din Azure AD enkel inloggning för konfiguration.

Öppna ett webbläsarfönster i ett ”InPrivate” eller ”Incognito” läge för att utlösa en ny autentisering.  Använd Ctrl + Skift + P i Internet Explorer.  I Chrome, använder du Ctrl + Skift + N.  Bläddra till en skyddad resurs och utföra en Azure AD-kontoinloggning i privat webbläsarfönster.  Efter genomförd inloggning tas du till den begärda platsen i en session för isolering.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/menlosecurity-tutorial/tutorial_general_01.png
[2]: ./media/menlosecurity-tutorial/tutorial_general_02.png
[3]: ./media/menlosecurity-tutorial/tutorial_general_03.png
[4]: ./media/menlosecurity-tutorial/tutorial_general_04.png

[100]: ./media/menlosecurity-tutorial/tutorial_general_100.png

[200]: ./media/menlosecurity-tutorial/tutorial_general_200.png
[201]: ./media/menlosecurity-tutorial/tutorial_general_201.png
[202]: ./media/menlosecurity-tutorial/tutorial_general_202.png
[203]: ./media/menlosecurity-tutorial/tutorial_general_203.png

