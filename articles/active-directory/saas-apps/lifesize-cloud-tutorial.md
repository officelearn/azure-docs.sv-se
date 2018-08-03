---
title: 'Självstudier: Azure Active Directory-integrering med Lifesize moln | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lifesize molnet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: c03456dcda2b3ee44686b070cdebb5fc81c3968c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449187"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Självstudier: Azure Active Directory-integration med Lifesize molnet

I den här självstudien får du lära dig hur du integrerar Lifesize molnet med Azure Active Directory (AD Azure).

Integrera Lifesize molnet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Lifesize moln
- Du kan aktivera användarna att automatiskt få loggat in på Lifesize Cloud (enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Lifesize molnet, behöver du följande objekt:

- En Azure AD-prenumeration
- En Lifesize enkel inloggning aktiverat molnprenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Lifesize molnet från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Att lägga till Lifesize molnet från galleriet
Om du vill konfigurera integreringen av Lifesize moln till Azure AD, som du behöver lägga till Lifesize molnet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Lifesize molnet från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Lifesize molnet**.

    ![Skapa en Azure AD-användare för testning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

1. I resultatpanelen väljer **Lifesize molnet**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Lifesize molnet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Lifesize molnet är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Lifesize molnet upprättas.

I Lifesize molnet, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Lifesize moln, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Lifesize molnet](#creating-a-lifesize-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Lifesize moln som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Lifesize molnprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Lifesize molnet:**

1. I Azure-portalen på den **Lifesize molnet** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

1. På den **Lifesize molnet domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://login.lifesizecloud.com/ls/?acs`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://login.lifesizecloud.com/<companyname>`

     
1. Kontrollera **visa avancerade URL-inställningar**, utföra följande steg:    
   
    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    I den **vidarebefordrar tillstånd** textrutan anger du ett URL med hjälp av följande mönster: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Observera att detta inte är det verkliga värdet. Du måste uppdatera dessa värden med de faktiska inloggnings-URL, Vidarebefordransstatus och identifierare. Kontakta [Lifesize Cloud klienten supportteamet](https://www.lifesize.com/support) att hämta inloggnings-URL och identifierar-värden och du får ut Vidarebefordransstatus från SSO-konfiguration som beskrivs senare i självstudien.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_general_400.png)

1. På den **Lifesize Molnkonfigurationen** klickar du på **konfigurera Lifesize molnet** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

1. Att hämta SSO konfigurerats för ditt program, logga in på Lifesize molnprogram med administratörsrättigheter.

1. Klicka på ditt namn i övre högra hörnet och klicka sedan på den **avancerade inställningar**.
   
    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

1. I avancerade inställningar nu klickar du på den **SSO-konfiguration** länk. Konfigurationssidan för enkel inloggning för din instans öppnas.
   
    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

1. Nu konfigurera följande värden i Konfigurationsgränssnittet för enkel inloggning.    
   
    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. I **Providerutgivare** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.

    b.  I **inloggnings-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Öppna din Base64-kodat certifikat i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan.
  
    d. Ange värdet som i SAML-attributmappningarna för textrutan förnamn **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. I SAML-attributmappning för den **efternamn** textrutan Ange värdet som **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. I SAML-attributmappning för den **e-post** textrutan Ange värdet som **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

1. Att kontrollera konfigurationen kan du klicka på den **Test** knappen.
   
    >[!NOTE]
    >För lyckad testning måste du slutföra guiden för konfiguration av i Azure AD och även ge åtkomst till användare eller grupper som kan utföra testet.

1. Aktivera SSO genom att kontrollera på den **aktivera SSO** knappen.

1. Nu klickar du på den **uppdatering** knapp så att alla inställningar har sparats. Detta genererar RelayState-värdet. Kopiera RelayState-värde, som genereras i textrutan, klistra in den i den **Vidarebefordransstatus** textrutan under **Lifesize molnet domän och URL: er** avsnittet. 

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/lifesize-cloud-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/lifesize-cloud-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/lifesize-cloud-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Skapa en testanvändare Lifesize molnet

I det här avsnittet skapar du en användare som kallas Britta Simon i Lifesize molnet. Lifesize molnet har stöd för automatisk användaretablering. Efter en lyckad autentisering på Azure AD kommer användaren att automatiskt tillhandahållas i programmet. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Lifesize moln.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Lifesize molnet, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Lifesize molnet**.

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Lifesize molnet i åtkomstpanelen, bör du få inloggningssidan i Lifesize molnprogram.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/lifesize-cloud-tutorial/tutorial_general_203.png

