---
title: 'Självstudier: Azure Active Directory-integrering med säkra leverera | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och skydda leverera.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: fccd5668-fe6f-4e6d-a9ce-ba4f321c33d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: c0c3fd0d57e0e8eed539369a81eeb64076e2f303
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151030"
---
# <a name="tutorial-azure-active-directory-integration-with-secure-deliver"></a>Självstudier: Azure Active Directory-integrering med säkra leverera

Lär dig hur du integrerar säkert leverera med Azure Active Directory (AD Azure) i den här självstudien.

Integrera säkert leverera med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till säker leverera
- Du kan aktivera användarna att automatiskt få loggat in på säkert leverera (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med säkra leverera, behöver du följande objekt:

- En Azure AD-prenumeration
- En säker leverera enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till säkert leverera från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-secure-deliver-from-the-gallery"></a>Att lägga till säkert leverera från galleriet
För att konfigurera integrering av säker leverera i Azure AD, som du behöver lägga till säkert leverera från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till säkert leverera från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **säkert leverera**.

    ![Skapa en Azure AD-användare för testning](./media/securedeliver-tutorial/tutorial_securedeliver_search.png)

1. I resultatpanelen väljer **säkert leverera**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/securedeliver-tutorial/tutorial_securedeliver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med säkra leverera baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i säkert leverera är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i säkert leverera upprättas.

I säkert leverera tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med säkra leverera, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en säker leverera testanvändare](#creating-a-secure-deliver-test-user)**  – du har en motsvarighet för Britta Simon i säkert leverera som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för säker leverera.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med säkra leverera:**

1. I Azure-portalen på den **säkert leverera** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/securedeliver-tutorial/tutorial_securedeliver_samlbase.png)

1. På den **säkert leverera domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/securedeliver-tutorial/tutorial_securedeliver_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.i-securedeliver.jp/sd/<tenantname>/jsf/login/sso`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<companyname>.i-securedeliver.jp/sd/<tenantname>/postResponse`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [säkert leverera klienten supportteamet](mailto:iw-sd-support@fujifilm.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/securedeliver-tutorial/tutorial_securedeliver_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/securedeliver-tutorial/tutorial_general_400.png)

1. På den **säkert leverera Configuration** klickar du på **konfigurera SECURE leverera** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/securedeliver-tutorial/tutorial_securedeliver_configure.png) 

1. Att konfigurera enkel inloggning på **säkert leverera** sida, som du behöver skicka de hämtade **certifikat (Base64)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [säkert leverera supportteamet](mailto:iw-sd-support@fujifilm.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/securedeliver-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/securedeliver-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/securedeliver-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/securedeliver-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-secure-deliver-test-user"></a>Skapa en säker leverera testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i säkert leverera. Arbeta med [säkert leverera supportteamet](mailto:iw-sd-support@fujifilm.com) att lägga till användare i säkert leverera-konto.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till säker leverera.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon att leverera säker, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **säkert leverera**.

    ![Konfigurera enkel inloggning](./media/securedeliver-tutorial/tutorial_securedeliver_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.  

När du klickar på panelen säkert leverera i åtkomstpanelen du bör få automatiskt loggat in på ditt säkra leverera program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securedeliver-tutorial/tutorial_general_01.png
[2]: ./media/securedeliver-tutorial/tutorial_general_02.png
[3]: ./media/securedeliver-tutorial/tutorial_general_03.png
[4]: ./media/securedeliver-tutorial/tutorial_general_04.png

[100]: ./media/securedeliver-tutorial/tutorial_general_100.png

[200]: ./media/securedeliver-tutorial/tutorial_general_200.png
[201]: ./media/securedeliver-tutorial/tutorial_general_201.png
[202]: ./media/securedeliver-tutorial/tutorial_general_202.png
[203]: ./media/securedeliver-tutorial/tutorial_general_203.png

