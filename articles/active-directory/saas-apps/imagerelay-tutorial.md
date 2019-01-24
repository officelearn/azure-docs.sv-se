---
title: 'Självstudier: Azure Active Directory-integrering med bild Relay | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och avbildning Relay.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 0671d2f5ad4be34926c8e3d2b22711c4af5fd435
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812105"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Självstudier: Azure Active Directory-integrering med bild Relay

Lär dig hur du integrerar bild Relay med Azure Active Directory (AD Azure) i den här självstudien.

Integrera bild Relay med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till avbildningen Relay
- Du kan aktivera användarna att automatiskt få loggat in på bild Relay (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med bild Relay behöver du följande objekt:

- En Azure AD-prenumeration
- En bild Relay enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Relay-avbildning från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-image-relay-from-the-gallery"></a>Att lägga till Relay-avbildning från galleriet
För att konfigurera integrering av avbildningen Relay i Azure AD, som du behöver lägga till Relay-avbildning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Relay-avbildning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **bild Relay**.

    ![Skapa en Azure AD-användare för testning](./media/imagerelay-tutorial/tutorial_imagerelay_search.png)

1. I resultatpanelen väljer **bild Relay**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med bild Relay baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i avbildningen Relay är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i avbildningen Relay upprättas.

I bild-reläer, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med bild Relay, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en avbildning Relay testanvändare](#creating-an-image-relay-test-user)**  – du har en motsvarighet för Britta Simon i avbildningen Relay som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din avbildning Relay-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med bild Relay:**

1. I Azure-portalen på den **bild Relay** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

1. På den **bild Relay domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.imagerelay.com/`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [bild Relay klienten supportteamet](http://support.imagerelay.com/) att hämta dessa värden. 
 


1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_general_400.png)

1. På den **Relay bildkonfiguration** klickar du på **konfigurera avbildningen Relay** att öppna **konfigurera inloggning** fönster. Kopiera den **tjänst-URL för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_configure.png) 

1. I ett nytt webbläsarfönster loggar du in din avbildning Relay företagets webbplats som administratör.

1. I verktygsfältet högst upp, klickar du på den **användare och behörigheter** arbetsbelastning.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Klicka på **Skapa ny behörighetsuppsättning**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

1. I den **enkel inloggning på inställningar** arbetsbelastningen, väljer den **den här gruppen kan bara logga in via enkel inloggning** och klicka sedan på **spara**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Gå till **kontoinställningar**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Gå till den **enkel inloggning på inställningar** arbetsbelastning.
    
     ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

1. På den **SAML-inställningar** dialogrutan utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. I **inloggnings-URL** textrutan klistra in värdet för **enkel inloggnings-URL för** som du har kopierat från Azure-portalen.

    b. I **URL för utloggning** textrutan klistra in värdet för **tjänst-URL för enkel utloggning** som du har kopierat från Azure-portalen.

    c. Som **Format för namn-Id**väljer **urn: oasis: namn: tc: SAML:1.1:nameid-format: e-postadress**.

    d. Som **bindning alternativ för begäranden från Service Provider (bild relä)** väljer **POST bindning**.

    e. Under **x.509 Certificate**, klickar du på **uppdatera certifikatet**.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Öppna det nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i textrutan för x.509-certifikat.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. I **just Användaretablering** väljer den **aktivera just Användaretablering**.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Välj behörighetsgruppen (till exempel **SSO grundläggande**) som tillåts logga in endast via enkel inloggning.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/imagerelay-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/imagerelay-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/imagerelay-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/imagerelay-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-image-relay-test-user"></a>Skapa en avbildning Relay testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i avbildningen Relay.

**Om du vill skapa en användare som kallas Britta Simon i avbildningen Relay, utför du följande steg:**

1. Inloggning till webbplatsen bild Relay företagets som administratör.

1. Gå till **användare och behörigheter** och välj **skapa SSO användare**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Ange den **e-post**, **Förnamn**, **efternamn**, och **företagets** för den användare som du vill etablera och välj behörighetsgruppen (för exempel grundläggande SSO) vilket är den grupp som kan logga in endast via enkel inloggning.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Klicka på **Skapa**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till avbildningen relä.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon till bild-relä, gör du:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **bild Relay**.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.    

När du klickar på panelen bild Relay i åtkomstpanelen du bör få automatiskt loggat in på ditt bild Relay-program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/imagerelay-tutorial/tutorial_general_203.png

