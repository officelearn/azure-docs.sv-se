---
title: 'Självstudier: Azure Active Directory-integrering med Sprinklr | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a293535bbda5960dc39640fad1704ddd7390089f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195506"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Självstudier: Azure Active Directory-integrering med Sprinklr

I den här självstudien får du lära dig hur du integrerar Sprinklr med Azure Active Directory (AD Azure).

Integrera Sprinklr med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Sprinklr
- Du kan aktivera användarna att automatiskt få loggat in på Sprinklr (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Sprinklr, behöver du följande objekt:

- En Azure AD-prenumeration
- En Sprinklr enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Sprinklr från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sprinklr-from-the-gallery"></a>Att lägga till Sprinklr från galleriet
För att konfigurera integrering av Sprinklr i Azure AD, som du behöver lägga till Sprinklr från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Sprinklr från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Sprinklr**.

    ![Skapa en Azure AD-användare för testning](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

1. I resultatpanelen väljer **Sprinklr**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Sprinklr baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Sprinklr är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Sprinklr upprättas.

I Sprinklr, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Sprinklr, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Sprinklr](#creating-a-sprinklr-test-user)**  – du har en motsvarighet för Britta Simon i Sprinklr som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Sprinklr program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Sprinklr:**

1. I Azure-portalen på den **Sprinklr** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

1. På den **Sprinklr domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.sprinklr.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL och identifierare. Kontakta [Sprinklr klienten supportteamet](https://www.sprinklr.com/contact-us/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/sprinklr-tutorial/tutorial_general_400.png)

1. På den **Sprinklr Configuration** klickar du på **konfigurera Sprinklr** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

1. I ett annat webbläsarfönster logga du in på webbplatsen Sprinklr företag som administratör.

1. Gå till **Administration \> inställningar**.
   
    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **hantera Partner \> enkelinloggning** på den vänstra rutan.
   
    ![Hantera Partner](./media/sprinklr-tutorial/ic782908.png "hantera Partner")

1. Klicka på **+ Lägg till enkel inloggning Ons**.
   
    ![Enkel inloggningar](./media/sprinklr-tutorial/ic782909.png "enkel inloggningar")

1. På den **för enkelinloggning** utför följande steg:
   
    ![Enkel inloggningar](./media/sprinklr-tutorial/ic782910.png "enkel inloggningar")

    a. I textrutan **Name** (Namn) skriver du ett namn för organisationen (till exempel: *WAADSSOTest*).

    b. Välj **aktiverat**.

    c. Välj **använder nya SSO-certifikat**.
             
    e. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textrutan.

    f. Klistra in den **SAML entitets-ID** värde som du har kopierat från Azure-portalen till den **entitets-Id** textrutan.

    g. Klistra in den **SAML enkel inloggning för tjänst-URL** värde som du har kopierat från Azure-portalen till den **inloggnings-URL för identitetsprovider** textrutan.

    h. Klistra in den **URL: en för utloggning** värde som du har kopierat från Azure-portalen till den **utloggnings-URL för identitetsprovider** textrutan.
     
    i. Som **SAML-ID användartyp**väljer **Assertion innehåller användarens sprinklr.com användarnamn**.

    j. Som **SAML-ID Användarplats**väljer **användar-ID är i namnidentifierare elementet i instruktionen ämne**.

    k. Klicka på **Spara**.
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/sprinklr-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/sprinklr-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/sprinklr-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sprinklr-test-user"></a>Skapa en Sprinklr testanvändare

1. Logga in på webbplatsen Sprinklr företag som administratör.

1. Gå till **Administration \> inställningar**.
   
    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **hantera klienten \> användare** i den vänstra rutan.
   
    ![Inställningar](./media/sprinklr-tutorial/ic782914.png "Inställningar")

1. Klicka på **Lägg till användare**.
   
    ![Inställningar](./media/sprinklr-tutorial/ic782915.png "Inställningar")

1. På den **Redigera användare** dialogrutan utför följande steg:
   
    ![Redigera användare](./media/sprinklr-tutorial/ic782916.png "Redigera användare") 

    a. I den **e-post**, **Förnamn** och **efternamn** textrutor, skriver du informationen för en Azure AD-användarkonto som du vill etablera.

    b. Välj **lösenord inaktiverad**.

    c. Välj **språk**.

    d. Välj **användartyp**.

    e. Klicka på **Uppdatera**.
   
     >[!IMPORTANT]
     >**Lösenord inaktiveras** måste väljas för att aktivera en användare att logga in via en identitetsleverantör. 
     
1. Gå till **rollen**, och utför följande steg:
   
    ![Partner roller](./media/sprinklr-tutorial/ic782917.png "Partner roller")

    a. Från den **Global** väljer **alla\_behörigheter**.  

    b. Klicka på **Uppdatera**.

>[!NOTE]
>Du kan använda alla andra Sprinklr användare konto verktyg för att skapa eller API: er som tillhandahålls av Sprinklr att etablera användarkonton i Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sprinklr.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Sprinklr, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Sprinklr**.

    ![Konfigurera enkel inloggning](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Sprinklr i åtkomstpanelen du bör få automatiskt loggat in på programmets Sprinklr för mer information om åtkomstpanelen, se [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

