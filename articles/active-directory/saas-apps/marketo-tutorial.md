---
title: 'Självstudier: Azure Active Directory-integrering med Marketo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: dbdf66c71bf007b27751191d3818f8002a8c2614
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172569"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Självstudier: Azure Active Directory-integrering med Marketo

I den här självstudien får du lära dig hur du integrerar Marketo med Azure Active Directory (AD Azure).

Integrera Marketo med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Marketo
- Du kan aktivera användarna att automatiskt få loggat in på Marketo (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Marketo, behöver du följande objekt:

- En Azure AD-prenumeration
- En Marketo enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Marketo från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-marketo-from-the-gallery"></a>Att lägga till Marketo från galleriet
För att konfigurera integrering av Marketo i Azure AD, som du behöver lägga till Marketo från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Marketo från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Marketo**.

    ![Skapa en Azure AD-användare för testning](./media/marketo-tutorial/tutorial_marketo_search.png)

1. I resultatpanelen väljer **Marketo**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Marketo baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Marketo är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Marketo upprättas.

I Marketo, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Marketo, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Marketo](#creating-a-marketo-test-user)**  – du har en motsvarighet för Britta Simon i Marketo som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Marketo-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Marketo:**

1. I Azure-portalen på den **Marketo** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_samlbase.png)

1. På den **Marketo-domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://saml.marketo.com/sp`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Marketo-supportteamet](http://investors.marketo.com/contactus.cfm) att hämta dessa värden.
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_general_400.png)

1. På den **Marketo Configuration** klickar du på **konfigurera Marketo** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_configure.png) 

1. Logga in till Marketo med administratörsautentiseringsuppgifter för att få Munchkin ID: T för ditt program, och utföra följande åtgärder:
   
    a. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn integrering och klicka på den **Munchkin länk**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopiera Munchkin-Id som visas på skärmen och slutför svars-URL i guiden för konfiguration av Azure AD.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_12.png) 

1. Så här konfigurerar du SSO i programmet i stegen nedan:
   
    a. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn integrering och klicka på **Single Sign On**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Om du vill aktivera SAML-inställningar klickar du på **redigera** knappen.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Aktiverad** inställningar för enkel inloggning.
   
    f. Klistra in den **SAML entitets-ID**i den **utfärdar-ID** textrutan.
   
    g. I den **entitets-ID** textrutan anger du Webbadressen som `http://saml.marketo.com/sp`.
   
    h. Välj den plats som ID **namnidentifierare elementet**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Om ditt användar-ID inte är UPN-värdet och sedan ändra värdet i fliken attribut.
   
    i. Ladda upp certifikatet som du har laddat ned från Azure AD-konfigurationsguiden. **Spara** inställningarna.
   
    j. Redigera inställningar för omdirigera felsidor.
   
    k. Klistra in den **SAML enkel inloggning för tjänst-URL** i den **inloggnings-URL** textrutan.
   
    l. Klistra in den **URL: en för utloggning** i den **URL för utloggning** textrutan.
   
    m. I den **fel URL**, kopiera din **Marketo instans-URL** och klicka på **spara** för att spara inställningarna.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_10.png)

1. Om du vill aktivera enkel inloggning för användare att utföra följande åtgärder:
   
    a. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.
   
    b. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigera till den **Security** menyn och klickar på **inloggningsinställningar**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Kontrollera den **kräver SSO** alternativet och **spara** inställningarna.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/marketo-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/marketo-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/marketo-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/marketo-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-marketo-test-user"></a>Skapa en testanvändare i Marketo

I det här avsnittet skapar du en användare som kallas Britta Simon i Marketo. Följ dessa steg om du vill skapa en användare i Marketo-plattformen.

1. Logga in på Marketo-app med hjälp av autentiseringsuppgifter som administratör.

1. Klicka på den **Admin** knappen i det övre navigeringsfönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 

1. Navigera till den **Security** menyn och klickar på **användare och roller**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_19.png)  

1. Klicka på den **Bjud in ny användare** länken på fliken användare
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_15.png) 

1. I guiden bjuda in nya användare fyller du följande information
   
    a. Ange användaren **e-post** adressen i textrutan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Ange den **Förnamn** i textrutan
   
    c. Ange den **efternamn** i textrutan
   
    d. Klicka på **Nästa**

1. I den **behörigheter** fliken den **userRoles** och klicka på **nästa**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_17.png)
1. Klicka på den **skicka** knappen för att skicka användarinbjudan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_18.png)

1. Användaren tar emot e-postmeddelandet och klicka på länken och ändra lösenordet för att aktivera kontot. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Marketo.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Marketo, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Marketo**.

    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Marketo i åtkomstpanelen du bör få automatiskt loggat in på ditt Marketo-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/marketo-tutorial/tutorial_general_01.png
[2]: ./media/marketo-tutorial/tutorial_general_02.png
[3]: ./media/marketo-tutorial/tutorial_general_03.png
[4]: ./media/marketo-tutorial/tutorial_general_04.png

[100]: ./media/marketo-tutorial/tutorial_general_100.png

[200]: ./media/marketo-tutorial/tutorial_general_200.png
[201]: ./media/marketo-tutorial/tutorial_general_201.png
[202]: ./media/marketo-tutorial/tutorial_general_202.png
[203]: ./media/marketo-tutorial/tutorial_general_203.png

