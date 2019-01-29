---
title: 'Självstudier: Azure Active Directory-integrering med PolicyStat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 456d66b87f626391f6e81ffd7be244381f0978b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155994"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Självstudier: Azure Active Directory-integrering med PolicyStat

I den här självstudien får du lära dig hur du integrerar PolicyStat med Azure Active Directory (AD Azure).

Integrera PolicyStat med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PolicyStat
- Du kan aktivera användarna att automatiskt få loggat in på PolicyStat (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PolicyStat, behöver du följande objekt:

- En Azure AD-prenumeration
- En PolicyStat enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till PolicyStat från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-policystat-from-the-gallery"></a>Att lägga till PolicyStat från galleriet
För att konfigurera integrering av PolicyStat i Azure AD, som du behöver lägga till PolicyStat från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PolicyStat från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **PolicyStat**.

    ![Skapa en Azure AD-användare för testning](./media/policystat-tutorial/tutorial_policystat_search.png)

1. I resultatpanelen väljer **PolicyStat**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PolicyStat baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i PolicyStat är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i PolicyStat upprättas.

I PolicyStat, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med PolicyStat, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare PolicyStat](#creating-a-policystat-test-user)**  – du har en motsvarighet för Britta Simon i PolicyStat som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PolicyStat program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PolicyStat:**

1. I Azure-portalen på den **PolicyStat** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

1. På den **PolicyStat domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.policystat.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [PolicyStat klienten supportteamet](http://www.policystat.com/support/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

1. Målet med det här avsnittet som beskriver hur du aktiverar användare att autentisera till PolicyStat med ett konto i Azure AD med federation baserat på SAML-protokoll.

    PolicyStat programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar till din **SAML-Tokenattribut** konfiguration.  

     Följande skärmbild visar ett exempel på detta.

     ![Attribut](./media/policystat-tutorial/tutorial_policystat_attribute.png "Attribut")

1. Om du vill lägga till de nödvändiga attributmappningarna, utför du följande steg:

    | Attributnamn    |   Attributvärde |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Konfigurera enkel inloggning](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. I den **attributnamnet** textrutan typ **uid**.

    c. I den **attributvärdet** textrutan väljer **ExtractMailPrefix()**.    
   
    d. Från den **e** väljer **User.mail**.
    
    e. Klicka på **Ok**

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/policystat-tutorial/tutorial_general_400.png)

1. Logga in på webbplatsen PolicyStat företag som en administratör i ett annat webbläsarfönster.

1. Klicka på den **Admin** fliken och klicka sedan på **konfigurationen för enkel inloggning** i vänstra navigeringsfönstret.
   
    ![Administratör-menyn](./media/policystat-tutorial/ic808633.png "administratör-menyn")

1. I den **installationsprogrammet** väljer **integrering för aktivera enkel inloggning**.
   
    ![Enkel Inloggningkonfigurationen](./media/policystat-tutorial/ic808634.png "enkel Inloggningkonfigurationen")

1. Klicka på **Konfigurera attribut**, och klicka sedan på **Konfigurera attribut** avsnittet, utför följande steg:
   
    ![Enkel Inloggningkonfigurationen](./media/policystat-tutorial/ic808635.png "enkel Inloggningkonfigurationen")
   
    a. I den **Användarnamnattribut** textrutan typ **uid**.

    b. I den **förnamn attributet** textrutan typ **firstname** användarens **Britta**.

    c. I den **senaste namnattributet** textrutan typ **lastname** användarens **Simon**.

    d. I den **e-attributet** textrutan typ **e-postadress** användarens **BrittaSimon@contoso.com**.

    e. Klicka på **Spara ändringar**.

1. Klicka på **Your IDP-Metadata**, och klicka sedan på **Your IDP-Metadata** avsnittet, utför följande steg:
   
    ![Enkel Inloggningkonfigurationen](./media/policystat-tutorial/ic808636.png "enkel Inloggningkonfigurationen")
   
    a. Öppna din hämtade metadatafilen, kopiera innehållet och klistra in den i den **din identitet providern Metadata** textrutan.

    b. Klicka på **Spara ändringar**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/policystat-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/policystat-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/policystat-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/policystat-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-policystat-test-user"></a>Skapa en PolicyStat testanvändare

För att aktivera Azure AD-användare att logga in på PolicyStat, måste de etableras i PolicyStat.  

PolicyStat stöder just-in-time användaretablering. Det innebär att du behöver inte lägga till användare manuellt till PolicyStat. Användarna kommer automatiskt att hämta läggas på sin första inloggning via enkel inloggning.

>[!NOTE]
>Du kan använda alla andra PolicyStat användare konto verktyg för att skapa eller API: er som tillhandahålls av PolicyStat att etablera användarkonton i Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PolicyStat.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon PolicyStat, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **PolicyStat**.

    ![Konfigurera enkel inloggning](./media/policystat-tutorial/tutorial_policystat_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen PolicyStat i åtkomstpanelen du bör få automatiskt loggat in på ditt PolicyStat program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

