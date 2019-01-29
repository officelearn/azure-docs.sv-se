---
title: 'Självstudier: Azure Active Directory-integrering med plattare filer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och plattare filer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: fe15a1aa0d284913c77d62e254b8827d9d60c850
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188243"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Självstudier: Azure Active Directory-integrering med plattare filer

I den här självstudien får du lära dig hur du integrerar plattare filer med Azure Active Directory (AD Azure).

Integrera plattare filer med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till plattare filer
- Du kan aktivera användarna att automatiskt få loggat in på plattare filer (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med plattare filer, behöver du följande objekt:

- En Azure AD-prenumeration
- En plattare filer enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till plattare filer från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-flatter-files-from-the-gallery"></a>Lägga till plattare filer från galleriet
Om du vill konfigurera integreringen av plattare filer till Azure AD, som du behöver lägga till plattare filer från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till plattare filer från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **plattare filer**.

    ![Skapa en Azure AD-användare för testning](./media/flatter-files-tutorial/tutorial_flatterfiles_search.png)

1. I resultatpanelen väljer **plattare filer**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med plattare filer baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i plattare filer till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i plattare filer upprättas.

Plattare filer, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med plattare filer, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare plattare filer](#creating-a-flatter-files-test-user)**  – du har en motsvarighet för Britta Simon i plattare filer som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för plattare filer.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med plattare filer:**

1. I Azure-portalen på den **plattare filer** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

1. På den **plattare filer domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_general_400.png)

1. På den **plattare filer Configuration** klickar du på **konfigurera plattare filer** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

1. Inloggning till programmet plattare filer som en administratör.

1. Klicka på **INSTRUMENTPANELEN**. 
   
    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

1. Klicka på **inställningar**, och utför följande steg på den **företagets** fliken: 
   
    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Välj **använda SAML 2.0 för autentisering**.
    
    b. Klicka på **Konfigurera SAML**.

1. På den **SAML-konfiguration** dialogrutan utför följande steg: 
   
    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. I den **domän** textrutan skriver din registrerade domän.
   
    >[!NOTE]
    >Om du inte har en registrerad domän ännu, kontakta din plattare filer supportteam via [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. I **-URL för identitetsprovider** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat utgör Azure-portalen.
   
    c.  Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textrutan.

    d. Klicka på **Uppdatera**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/flatter-files-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/flatter-files-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/flatter-files-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/flatter-files-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-flatter-files-test-user"></a>Skapa en testanvändare plattare filer

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i plattare filer.

**Utför följande steg för att skapa en användare som kallas Britta Simon i plattare filer:**

1. Logga in på din **plattare filer** företagets plats som administratör.

1. I navigeringsfönstret till vänster klickar du på **inställningar**, och klicka sedan på den **användare** fliken.
   
    ![Skapa en plattare filer användare](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

1. Klicka på **Lägg till användare**. 

1. På den **Lägg till användare** dialogrutan utför följande steg:
   
    ![Skapa en plattare filer användare](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. I den **Förnamn** textrutan typ **Britta**.
   
    b. I den **efternamn** textrutan typ **Simon**. 
   
    c. I den **e-postadress** textrutan skriver Brittas e-postadress i Azure-portalen.
   
    d. Klicka på **Skicka**.   


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till plattare filer.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon plattare filer, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **plattare filer**.

    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatterfiles_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen plattare filer i åtkomstpanelen du bör få automatiskt loggat in på programmets plattare filer.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/flatter-files-tutorial/tutorial_general_203.png

