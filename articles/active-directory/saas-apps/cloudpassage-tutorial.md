---
title: 'Självstudier: Azure Active Directory-integrering med CloudPassage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d8bddc309db3ede67586a2067fcf57fbede7d6b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152798"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Självstudier: Azure Active Directory-integrering med CloudPassage

I den här självstudien får du lära dig hur du integrerar CloudPassage med Azure Active Directory (AD Azure).

Integrera CloudPassage med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till CloudPassage
- Du kan aktivera användarna att automatiskt få loggat in på CloudPassage (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med CloudPassage, behöver du följande objekt:

- En Azure AD-prenumeration
- En CloudPassage enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till CloudPassage från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cloudpassage-from-the-gallery"></a>Att lägga till CloudPassage från galleriet
För att konfigurera integrering av CloudPassage i Azure AD, som du behöver lägga till CloudPassage från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till CloudPassage från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **CloudPassage**.

    ![Skapa en Azure AD-användare för testning](./media/cloudpassage-tutorial/tutorial_cloudpassage_search.png)

1. I resultatpanelen väljer **CloudPassage**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med CloudPassage baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i CloudPassage är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i CloudPassage upprättas.

I CloudPassage, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med CloudPassage, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare CloudPassage](#creating-a-cloudpassage-test-user)**  – du har en motsvarighet för Britta Simon i CloudPassage som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt CloudPassage program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med CloudPassage:**

1. I Azure-portalen på den **CloudPassage** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

1. På den **CloudPassage domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://portal.cloudpassage.com/saml/init/accountid`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://portal.cloudpassage.com/saml/consume/accountid`. Du kan hämta värdet för det här attributet genom att klicka på **SSO installationsprogrammet dokumentation** i den **inställningar för enkel inloggning** på CloudPassage-portal.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL och inloggnings-URL. Kontakta [CloudPassage klienten supportteamet](https://www.cloudpassage.com/company/contact/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

1. Programmets CloudPassage förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel på detta.
   
   ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | Attributnamn | Attributvärde |
    | --- | --- |
    | förnamn |user.givenname |
    | lastname |user.surname |
    | e-post |user.mail |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_general_400.png)
    
1. På den **CloudPassage Configuration** klickar du på **konfigurera CloudPassage** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

1. I ett annat webbläsarfönster inloggning till webbplatsen CloudPassage företag som administratör.

1. Klicka på menyn längst upp **inställningar**, och klicka sedan på **Platsadministration**. 
   
    ![Konfigurera enkel inloggning][12]

1. Klicka på den **autentiseringsinställningar** fliken. 
   
    ![Konfigurera enkel inloggning][13]

1. I den **inställningar för enkel inloggning** avsnittet, utför följande steg: 
   
    ![Konfigurera enkel inloggning][14]

    a. Välj **aktivera enkel sign-on(SSO) (enkel inloggning installationsprogrammet dokumentation)** kryssrutan.
    
    b. Klistra in **SAML entitets-ID** till den **utfärdar-URL för SAML** textrutan.
  
    c. Klistra in **SAML enkel inloggning för tjänst-URL** till den **slutpunkts-URL för SAML** textrutan.
  
    d. Klistra in **URL: en för utloggning** till den **utloggning landningssida** textrutan.
  
    e. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet i nedladdade certifikatet till Urklipp och klistra in den i den **x 509-certifikat** textrutan.
  
    f. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/cloudpassage-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/cloudpassage-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/cloudpassage-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/cloudpassage-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Skapa en CloudPassage testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i CloudPassage.

**Om du vill skapa en användare som kallas Britta Simon i CloudPassage, utför du följande steg:**

1. Inloggning till din **CloudPassage** företagets plats som administratör. 

1. I verktygsfältet högst upp, klickar du på **inställningar**, och klicka sedan på **Platsadministration**. 
   
   ![Skapa en CloudPassage testanvändare][22] 

1. Klicka på den **användare** fliken och klicka sedan på **Lägg till ny användare**. 
   
   ![Skapa en CloudPassage testanvändare][23]

1. I den **Lägg till ny användare** avsnittet, utför följande steg: 
   
   ![Skapa en CloudPassage testanvändare][24]
    
    a. I den **Förnamn** textrutan skriver Britta. 
  
    b. I den **efternamn** textrutan skriver Simon.
  
    c. I den **användarnamn** textrutan den **e-post** textrutan och **Skriv e-** textrutan skriver Brittas användarnamnet i Azure AD.
  
    d. Som **åtkomsttyp**väljer **aktivera Halo Portal åtkomst**.
  
    e. Klicka på **Lägg till**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till CloudPassage.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon CloudPassage, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **CloudPassage**.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen CloudPassage i åtkomstpanelen du bör få automatiskt loggat in på ditt CloudPassage program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/cloudpassage-tutorial/tutorial_general_203.png

