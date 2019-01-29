---
title: 'Självstudier: Azure Active Directory-integrering med SilkRoad liv Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SilkRoad liv Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: fd5b4da77c31bd5e0c095f24bea2d6ed8b0e93ab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154133"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Självstudier: Azure Active Directory-integrering med SilkRoad liv Suite

I den här självstudien får du lära dig hur du integrerar SilkRoad liv Suite med Azure Active Directory (AD Azure).

Integrera SilkRoad liv Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SilkRoad liv Suite.
- Du kan aktivera användarna att automatiskt få loggat in på SilkRoad liv Suite (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SilkRoad liv Suite, behöver du följande objekt:

- En Azure AD-prenumeration
- En SilkRoad liv Suite enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SilkRoad liv Suite från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Att lägga till SilkRoad liv Suite från galleriet
Om du vill konfigurera integreringen av SilkRoad liv Suite till Azure AD, som du behöver lägga till SilkRoad liv Suite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SilkRoad liv Suite från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **SilkRoad liv Suite**väljer **SilkRoad liv Suite** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SilkRoad liv Suite i resultatlistan](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SilkRoad liv Suite baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i SilkRoad liv Suite är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SilkRoad liv Suite upprättas.

I SilkRoad liv Suite, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SilkRoad liv Suite, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare SilkRoad liv Suite](#create-a-silkroad-life-suite-test-user)**  – du har en motsvarighet för Britta Simon i SilkRoad liv Suite som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SilkRoad liv Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SilkRoad liv Suite:**

1. I Azure-portalen på den **SilkRoad liv Suite** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

1. På den **SilkRoad liv Suite domän och URL: er** avsnittet, utför följande steg:

    ![SilkRoad liv Suite domän och URL: er med enkel inloggning för information](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. I textrutan **Svars-URL** anger du en URL med följande mönster: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SilkRoad liv Suite klienten supportteamet](https://www.silkroad.com/locations/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
1. På den **SilkRoad liv Suite Configuration** klickar du på **konfigurera SilkRoad liv Suite** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![SilkRoad liv Suite-konfiguration](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

1. Inloggning till webbplatsen SilkRoad företag som administratör. 
 
    >[!NOTE] 
    > För att få åtkomst till programmet SilkRoad liv Suite autentisering för att konfigurera federation med Microsoft Azure AD kan du kontakta SilkRoad Support eller genom ditt ombud för SilkRoad tjänster.

1. Gå till **tjänstleverantör**, och klicka sedan på **Federation information**. 
   
    ![Azure AD enkel inloggning][10]

1. Klicka på **hämta Federationsmetadata**, och spara sedan metadatafilen på datorn.
   
    ![Azure AD enkel inloggning][11] 

1. I din **SilkRoad** programmet, klickar du på **autentisering källor**.
   
    ![Azure AD enkel inloggning][12] 

1. Klicka på **lägga till autentiseringskälla**. 
   
    ![Azure AD enkel inloggning][13] 

1. I den **lägga till autentiseringskälla** avsnittet, utför följande steg: 
   
    ![Azure AD enkel inloggning][14]
  
    a. Under **alternativ 2 - metadatafil**, klickar du på **Bläddra** att ladda upp den hämtade metadatafilen från Azure-portalen.
  
    b. Klicka på **skapa identitetsprovider som använder fildata**.

1. I den **autentisering källor** klickar du på **redigera**. 
    
     ![Azure AD enkel inloggning][15] 

1. På den **redigera autentiseringskälla** dialogrutan utför följande steg: 
    
     ![Azure AD enkel inloggning][16] 

    a. Som **aktiverad**väljer **Ja**.

    b. I den **EntityId** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
   
    c. I den **IdP beskrivning** textrutan anger du en beskrivning för din konfiguration (till exempel: *Azure AD SSO*).

    d. I den **metadatafil** textrutan ladda upp den **metadata** fil som du har hämtat från Azure-portalen.
  
    e. I den **IdP namn** textrutan anger du ett namn som är specifik för din konfiguration (till exempel: *Azure SP*).
  
    f. I den **Utloggning** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.

    g. I den **inloggnings-URL för** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    h. Klicka på **Spara**.

1. Inaktivera alla andra källor för autentisering. 
    
     ![Azure AD enkel inloggning][17]

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Skapa en testanvändare SilkRoad liv Suite

I det här avsnittet skapar du en användare som kallas Britta Simon i SilkRoad liv Suite. Arbeta med [SilkRoad liv Suite klienten supportteamet](https://www.silkroad.com/locations/) att lägga till användare i SilkRoad liv Suite-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till SilkRoad liv Suite.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon SilkRoad liv Suite, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **SilkRoad liv Suite**.

    ![Länken SilkRoad liv Suite i listan med program](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SilkRoad liv Suite i åtkomstpanelen du bör få automatiskt loggat in på ditt SilkRoad liv Suite-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
