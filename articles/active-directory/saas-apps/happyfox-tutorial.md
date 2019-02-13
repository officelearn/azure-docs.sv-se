---
title: 'Självstudier: Azure Active Directory-integrering med HappyFox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HappyFox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8204ee77-f64b-4fac-b64a-25ea534feac0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10185f21ea396cc90e165a5132c33a0d7e695058
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167600"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>Självstudier: Azure Active Directory-integrering med HappyFox

I den här självstudien får du lära dig hur du integrerar HappyFox med Azure Active Directory (AD Azure).

Integrera HappyFox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till HappyFox
- Du kan aktivera användarna att automatiskt få loggat in på HappyFox (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med HappyFox, behöver du följande objekt:

- En Azure AD-prenumeration
- En HappyFox enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till HappyFox från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-happyfox-from-the-gallery"></a>Att lägga till HappyFox från galleriet
För att konfigurera integrering av HappyFox i Azure AD, som du behöver lägga till HappyFox från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HappyFox från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **HappyFox**.

    ![Skapa en Azure AD-användare för testning](./media/happyfox-tutorial/tutorial_happyfox_search.png)

1. I resultatpanelen väljer **HappyFox**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/happyfox-tutorial/tutorial_happyfox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HappyFox baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i HappyFox är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i HappyFox upprättas.

I HappyFox, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med HappyFox, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare HappyFox](#creating-a-happyfox-test-user)**  – du har en motsvarighet för Britta Simon i HappyFox som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt HappyFox program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med HappyFox:**

1. I Azure-portalen på den **HappyFox** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_samlbase.png)

1. På den **HappyFox domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.happyfox.com/`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.happyfox.com/saml/metadata/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [HappyFox klienten supportteamet](https://support.happyfox.com/home) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_general_400.png)

1. På den **HappyFox Configuration** klickar du på **konfigurera HappyFox** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet**.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_configure.png) 

1. Logga in på din HappyFox personal portal och gå till **hantera**, klicka på **integreringar** fliken.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/header.png) 

1. Klicka på fliken integreringar **konfigurera** under **SAML-integrering** att öppna enkel inloggning på inställningarna.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/configure.png) 

1. I avsnittet för SAML-konfiguration, klistra in den **SAML enkel inloggning för tjänst-URL** som du har kopierat från Azure-portalen i **mål-URL för enkel inloggning** textrutan.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/targeturl.png)

1. Öppna certifikatet hämtas från Azure-portalen i anteckningar och klistra in innehållet i **IdP signatur** avsnittet.
 
    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/cert.png)

1. Klicka på **Spara inställningar** knappen.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/savesettings.png)

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/happyfox-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/happyfox-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/happyfox-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/happyfox-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-happyfox-test-user"></a>Skapa en HappyFox testanvändare

Programmet stöder bara i tid användaretablering och -autentiserade användare skapas automatiskt i programmet.
        
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HappyFox.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon HappyFox, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **HappyFox**.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

1. När du klickar på panelen HappyFox i åtkomstpanelen, bör du få inloggningssidan i HappyFox program. Du bör se den **'SAML-** på sidan logga in.

    ![Plugin](./media/happyfox-tutorial/saml.png) 

1. Klicka på den **'SAML'** knappen för att logga in på HappyFox med hjälp av Azure AD-konto.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/happyfox-tutorial/tutorial_general_01.png
[2]: ./media/happyfox-tutorial/tutorial_general_02.png
[3]: ./media/happyfox-tutorial/tutorial_general_03.png
[4]: ./media/happyfox-tutorial/tutorial_general_04.png

[100]: ./media/happyfox-tutorial/tutorial_general_100.png

[200]: ./media/happyfox-tutorial/tutorial_general_200.png
[201]: ./media/happyfox-tutorial/tutorial_general_201.png
[202]: ./media/happyfox-tutorial/tutorial_general_202.png
[203]: ./media/happyfox-tutorial/tutorial_general_203.png

