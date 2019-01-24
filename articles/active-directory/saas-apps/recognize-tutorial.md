---
title: 'Självstudier: Azure Active Directory-integrering med identifiera | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och identifiera.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 49d501a07f2efa6e9c6e20dfe2a026badf13f624
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824158"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Självstudier: Azure Active Directory-integrering med identifiera

Lär dig hur du integrerar identifiera med Azure Active Directory (AD Azure) i den här självstudien.

Integrera identifiera med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till identifiera
- Du kan aktivera användarna att automatiskt få loggat in på identifiera (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med identifiera, behöver du följande objekt:

- En Azure AD-prenumeration
- En identifiera enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du få en tre månaders kostnadsfri utvärdering här: [Utvärderingserbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till identifiera från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-recognize-from-the-gallery"></a>Att lägga till identifiera från galleriet
För att konfigurera integrering av identifiera i Azure AD, som du behöver lägga till identifiera från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till identifiera från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **identifiera**.

    ![Skapa en Azure AD-användare för testning](./media/recognize-tutorial/tutorial_recognize_search.png)

1. I resultatpanelen väljer **identifiera**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med identifiera baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i identifiera är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i identifiera upprättas.

I identifiera, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med identifiera, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare identifiera](#creating-a-recognize-test-user)**  – du har en motsvarighet för Britta Simon i identifiera som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för identifiera.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med identifiera:**

1. I Azure-portalen på den **identifiera** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/recognize-tutorial/tutorial_recognize_samlbase.png)

1. På den **identifiera domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/recognize-tutorial/tutorial_recognize_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://recognizeapp.com/<your-domain>/saml/sso`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [identifiera klienten supportteamet](mailto:support@recognizeapp.com) att hämta inloggnings-URL och du kan hämta ID-värde genom att öppna URL: en för Service Provider Metadata från avsnittet Inställningar för enkel inloggning som beskrivs senare i självstudien. . 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/recognize-tutorial/tutorial_recognize_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/recognize-tutorial/tutorial_general_400.png)

1. På den **identifiera konfigurationen** klickar du på **konfigurera identifiera** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/recognize-tutorial/tutorial_recognize_configure.png) 

1. I ett annat webbläsarfönster inloggning för att identifiera klienten som administratör.

1. I det övre högra hörnet, klickar du på **menyn**. Gå till **företagets administratör**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/recognize-tutorial/tutorial_recognize_000.png)

1. I det vänstra navigeringsfönstret klickar du på **inställningar**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/recognize-tutorial/tutorial_recognize_001.png)

1. Utför följande steg på **inställningar för enkel inloggning** avsnittet.
   
    ![Konfigurera enkel inloggning på App-sida](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Som **aktivera SSO**väljer **på**.

    b. I den **IDP entitets-ID** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
    
    c. I den **mål-url för enkel inloggning** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
    
    d. I den **Slo-mål-url** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen. 
    
    e. Öppna din hämtade **certifikat (Base64)** filen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat** textrutan.
    
    f. Klicka på den **spara inställningarna för** knappen. 

1. Bredvid den **inställningar för enkel inloggning** avsnittet, Kopiera URL-Adressen under **Service Provider Metadata-url**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/recognize-tutorial/tutorial_recognize_003.png)

1. Öppna den **Metadata-URL-länk** under en tom webbläsare för att hämta för Metadatadokumentet. Kopiera sedan EntityDescriptor value(entityID) från filen och klistra in det i **identifierare** -textrutan i **identifiera domän och URL: er avsnittet** på Azure-portalen.
    
    ![Konfigurera enkel inloggning på App-sida](./media/recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/recognize-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/recognize-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/recognize-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/recognize-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-recognize-test-user"></a>Skapa en testanvändare identifiera

För att aktivera Azure AD-användare att logga in på identifiera etableras de i identifiera. När det gäller identifiera är etablering en manuell aktivitet.

Den här appen har inte stöd för SCIM-etablering men har en annan användare sync som etablerar användare. 

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen för Företagsportalen identifiera som administratör.

1. I det övre högra hörnet, klickar du på **menyn**. Gå till **företagets administratör**.

1. I det vänstra navigeringsfönstret klickar du på **inställningar**.

1. Utför följande steg på **användaren synkronisering** avsnittet.
   
   ![Ny användare](./media/recognize-tutorial/tutorial_recognize_005.png "Ny användare")
   
   a. Som **aktiverad för synkronisering av**väljer **på**.
   
   b. Som **Välj synkronisering providern**väljer **Microsoft / Office 365**.
   
   c. Klicka på **kör synkronisering av användare**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till identifiera.

![Tilldela användare][200] 

**Om du vill tilldela identifiera Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **identifiera**.

    ![Konfigurera enkel inloggning](./media/recognize-tutorial/tutorial_recognize_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen identifiera i åtkomstpanelen du bör få automatiskt loggat in på identifiera programmet. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/recognize-tutorial/tutorial_general_01.png
[2]: ./media/recognize-tutorial/tutorial_general_02.png
[3]: ./media/recognize-tutorial/tutorial_general_03.png
[4]: ./media/recognize-tutorial/tutorial_general_04.png

[100]: ./media/recognize-tutorial/tutorial_general_100.png

[200]: ./media/recognize-tutorial/tutorial_general_200.png
[201]: ./media/recognize-tutorial/tutorial_general_201.png
[202]: ./media/recognize-tutorial/tutorial_general_202.png
[203]: ./media/recognize-tutorial/tutorial_general_203.png

