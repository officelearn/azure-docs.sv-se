---
title: 'Självstudier: Azure Active Directory-integrering med Intacct | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: d7075894bde20f56c9323568ceffe8cad4118cbc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810099"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Självstudier: Azure Active Directory-integrering med Intacct

I den här självstudien får du lära dig hur du integrerar Intacct med Azure Active Directory (AD Azure).

Integrera Intacct med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Intacct
- Du kan aktivera användarna att automatiskt få loggat in på Intacct (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Intacct, behöver du följande objekt:

- En Azure AD-prenumeration
- En Intacct enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Intacct från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-intacct-from-the-gallery"></a>Att lägga till Intacct från galleriet
För att konfigurera integrering av Intacct i Azure AD, som du behöver lägga till Intacct från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Intacct från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Intacct**.

    ![Skapa en Azure AD-användare för testning](./media/intacct-tutorial/tutorial_intacct_search.png)

1. I resultatpanelen väljer **Intacct**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Intacct baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Intacct är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Intacct upprättas.

I Intacct, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Intacct, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Intacct](#creating-an-intacct-test-user)**  – du har en motsvarighet för Britta Simon i Intacct som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Intacct program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Intacct:**

1. I Azure-portalen på den **Intacct** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_samlbase.png)

1. På den **Intacct domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_url.png)

    I textrutan **Svars-URL** anger du en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska svars-URL. Kontakta [Intacct supportteamet](https://us.intacct.com/support) att hämta det här värdet.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_general_400.png)

1. På den **Intacct Configuration** klickar du på **konfigurera Intacct** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_configure.png) 

1. I ett annat webbläsarfönster, loggar du in din Intacct företagets webbplats som administratör.

1. Klicka på den **företagets** fliken och klicka sedan på **företagets information**.

    ![Företagets](./media/intacct-tutorial/ic790037.png "företag")

1. Klicka på den **Security** fliken och klicka sedan på **redigera**.

    ![Säkerhet](./media/intacct-tutorial/ic790038.png "Säkerhet")

1. I den **enkel inloggning (SSO)** avsnittet, utför följande steg:

    ![Enkel inloggning](./media/intacct-tutorial/ic790039.png "enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. Som **identifiera providertyp**väljer **SAML 2.0**.

    c. I **utfärdar-URL** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
   
    d. I **inloggnings-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    e. Öppna din **Base64-** kodade certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat** box.
   
    f. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/intacct-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/intacct-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/intacct-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/intacct-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-intacct-test-user"></a>Skapa en Intacct testanvändare

Om du vill konfigurera Azure AD-användare så att de kan logga in på Intacct, måste de etableras i Intacct. För Intacct är etablering en manuell aktivitet.

**Utför följande steg för att etablera användarkonton:**

1. Logga in på din **Intacct** klient.

1. Klicka på den **företagets** fliken och klicka sedan på **användare**.

    ![Användare](./media/intacct-tutorial/ic790041.png "Användare")
1. Klicka på den **Lägg till** fliken.

    ![Lägg till](./media/intacct-tutorial/ic790042.png "Lägg till")
1. I den **användarinformation** avsnittet, utför följande steg:

    ![Användarinformation](./media/intacct-tutorial/ic790043.png "användarinformation")

    a. Ange den **användar-ID**, **efternamn**, **Förnamn**, **e-postadress**, **rubrik**, och **Phone** på ett Azure AD-konto som du vill etablera i den **användarinformation** avsnittet.

    b. Välj den **administratörsprivilegier** på ett Azure AD-konto som du vill etablera.
   
    c. Klicka på **Spara**. Azure AD-kontoinnehavare tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den blir aktiv.

>[!NOTE]
>Du kan använda andra verktyg för Intacct att skapa användaren-konto eller API: er som tillhandahålls av Intacct för att etablera användarkonton i Azure AD.
        
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Intacct.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Intacct, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Intacct**.

    ![Konfigurera enkel inloggning](./media/intacct-tutorial/tutorial_intacct_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Intacct i åtkomstpanelen, bör det vara loggas in automatiskt till Intacct programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/intacct-tutorial/tutorial_general_01.png
[2]: ./media/intacct-tutorial/tutorial_general_02.png
[3]: ./media/intacct-tutorial/tutorial_general_03.png
[4]: ./media/intacct-tutorial/tutorial_general_04.png

[100]: ./media/intacct-tutorial/tutorial_general_100.png

[200]: ./media/intacct-tutorial/tutorial_general_200.png
[201]: ./media/intacct-tutorial/tutorial_general_201.png
[202]: ./media/intacct-tutorial/tutorial_general_202.png
[203]: ./media/intacct-tutorial/tutorial_general_203.png

