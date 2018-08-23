---
title: 'Självstudier: Azure Active Directory-integration med Intralinks | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Intralinks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 44cae95cfd01f8d6fbd6ddb4a11e9af290042ffa
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42057301"
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Självstudier: Azure Active Directory-integration med Intralinks

I den här självstudien får du lära dig hur du integrerar Intralinks med Azure Active Directory (AD Azure).

Integrera Intralinks med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Intralinks
- Du kan aktivera användarna att automatiskt få loggat in på Intralinks (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Intralinks, behöver du följande objekt:

- En Azure AD-prenumeration
- En Intralinks enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Intralinks från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-intralinks-from-the-gallery"></a>Att lägga till Intralinks från galleriet
För att konfigurera integrering av Intralinks i Azure AD, som du behöver lägga till Intralinks från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Intralinks från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Intralinks**.

    ![Skapa en Azure AD-användare för testning](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. I resultatpanelen väljer **Intralinks**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Intralinks baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Intralinks är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Intralinks upprättas.

I Intralinks, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Intralinks, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Intralinks](#creating-an-intralinks-test-user)**  – du har en motsvarighet för Britta Simon i Intralinks som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Intralinks program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Intralinks:**

1. I Azure-portalen på den **Intralinks** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_intralinks_samlbase.png)

1. På den **Intralinks domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_intralinks_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster:  `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Intralinks klienten supportteamet](https://www.intralinks.com/contact) att hämta det här värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_intralinks_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Intralinks** sida, som du behöver skicka de hämtade **XML-Metadata för** [Intralinks supportteam](https://www.intralinks.com/contact). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/intralinks-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/intralinks-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/intralinks-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/intralinks-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-intralinks-test-user"></a>Skapa en Intralinks testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Intralinks. Kontakta [Intralinks supportteam](https://www.intralinks.com/contact) att lägga till användare i Intralinks-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Intralinks.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Intralinks, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Intralinks**.

    ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_intralinks_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="add-intralinks-via-or-elite-application"></a>Lägg till Intralinks VIA eller Elite program

Intralinks använder samma SSO-identitetsplattformen för alla andra Intralinks program exklusive avtal Nexus program. Så om du planerar att använda andra Intralinks program sedan du först konfigurera enkel inloggning för en primär Intralinks program med hjälp av proceduren som beskrivs ovan.

Därefter kan du följa den nedan hur du lägger till ett annat Intralinks program i din klient som kan dra nytta av den här primära programmet för enkel inloggning. 

>[!NOTE]
>Den här funktionen är endast tillgängligt för kunder för Azure AD Premium-SKU och inte tillgängligt för kunder med kostnadsfria eller grundläggande SKU.

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]


1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Intralinks**.

    ![Skapa en Azure AD-användare för testning](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. På **Intralinks Lägg till app** utför följande steg:

    ![Lägga till Intralinks VIA eller Elite program](./media/intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. I **namn** textrutan, t.ex. Ange rätt namn på programmet **Intralinks Elite**.

    b. Klicka på **Lägg till** knappen.

1.  I Azure-portalen på den **Intralinks** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **inloggning länkade**.
 
    ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

1. Hämta SP initierad SSO URL från [Intralinks team](https://www.intralinks.com/contact) för andra Intralinks programmet och ange den i **konfigurera inloggnings-URL** enligt nedan. 
    
     ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     I inloggning på URL-textrutan skriver du URL: en som används av användarna till inloggning till Intralinks programmet med följande mönster:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/intralinks-tutorial/tutorial_general_400.png)

1. Tilldela program till användare eller grupper som visas i avsnittet  **[tilldelar Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Intralinks i åtkomstpanelen du bör få automatiskt loggat in på ditt Intralinks program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/intralinks-tutorial/tutorial_general_01.png
[2]: ./media/intralinks-tutorial/tutorial_general_02.png
[3]: ./media/intralinks-tutorial/tutorial_general_03.png
[4]: ./media/intralinks-tutorial/tutorial_general_04.png

[100]: ./media/intralinks-tutorial/tutorial_general_100.png

[200]: ./media/intralinks-tutorial/tutorial_general_200.png
[201]: ./media/intralinks-tutorial/tutorial_general_201.png
[202]: ./media/intralinks-tutorial/tutorial_general_202.png
[203]: ./media/intralinks-tutorial/tutorial_general_203.png

