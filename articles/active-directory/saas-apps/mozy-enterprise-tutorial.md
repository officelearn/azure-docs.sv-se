---
title: 'Självstudier: Azure Active Directory-integrering med Mozy Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9637212d97438d58a003ecf8d840ed887c7f5e35
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198612"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Självstudier: Azure Active Directory-integrering med Mozy Enterprise

Lär dig hur du integrerar Mozy Enterprise med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Mozy Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Mozy Enterprise
- Du kan aktivera användarna att automatiskt få loggat in på Mozy Enterprise (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mozy Enterprise, behöver du följande objekt:

- En Azure AD-prenumeration
- En Mozy Enterprise enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Mozy Enterprise från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Att lägga till Mozy Enterprise från galleriet
Om du vill konfigurera integreringen av Mozy Enterprise till Azure AD, som du behöver lägga till Mozy Enterprise från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mozy Enterprise från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Mozy Enterprise**.

    ![Skapa en Azure AD-användare för testning](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

1. I resultatpanelen väljer **Mozy Enterprise**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mozy Enterprise utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Mozy Enterprise till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Mozy företag upprättas.

I Mozy Enterprise, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mozy Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i Mozy företag som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Mozy Enterprise-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Mozy Enterprise:**

1. I Azure-portalen på den **Mozy Enterprise** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

1. På den **Mozy Enterprise domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Mozy Enterprise Client supportteamet](http://support.mozy.com/) att hämta det här värdet.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/mozy-enterprise-tutorial/tutorial_general_400.png)

1. På den **Mozy företagskonfiguration** klickar du på **konfigurera Mozy Enterprise** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

1. Logga in på ditt företag Mozy Företagsplats som administratör i ett annat webbläsarfönster.

1. I den **Configuration** klickar du på **autentiseringsprincip**.
   
   ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777314.png "autentiseringsprincip")

1. På den **autentiseringsprincip** avsnittet, utför följande steg:
   
   ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777315.png "autentiseringsprincip")
   
   a. Välj **katalogtjänsten** som **Provider**.
   
   b. Välj **använder LDAP Push**.
   
   c. Klicka på fliken **SAML-autentisering**.
   
   d. Klistra in **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen till den **autentiserings-URL för** textrutan.
   
   e. Klistra in **SAML entitets-ID**, som du har kopierat från Azure-portalen till den **SAML Endpoint** textrutan.
   
   f. Öppna din hämtade Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet i **SAML-certifikatet** textrutan.
   
   g. Välj **aktivera SSO för administratörer att logga in med sina nätverksinloggningsuppgifter**.
   
   h. Klicka på **Spara ändringar**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/mozy-enterprise-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/mozy-enterprise-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/mozy-enterprise-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Skapa en testanvändare Mozy Enterprise

För att aktivera Azure AD-användare att logga in på Mozy Enterprise, måste de etableras i Mozy Enterprise. När det gäller Mozy Enterprise är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda alla andra Mozy Enterprise användare konto verktyg för att skapa eller API: er som tillhandahålls av Mozy Enterprise att etablera AAD-användarkonton.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Mozy Enterprise** klient.

1. Klicka på **användare**, och klicka sedan på **Lägg till ny användare**.
   
   ![Användare](./media/mozy-enterprise-tutorial/ic777317.png "Användare")
   
   >[!NOTE]
   >Den **Lägg till ny användare** alternativet visas bara om **Mozy** väljs som providern under **autentiseringsprincip**. Om SAML-autentisering har konfigurerats, sedan läggs användarna till automatiskt på sin första inloggning via enkel inloggning på.
    
1. I användardialogrutan ny utför du följande steg:
   
   ![Lägga till användare](./media/mozy-enterprise-tutorial/ic777318.png "lägga till användare")
   
   a. Från den **välja en grupp** väljer du en grupp.
   
   b. Från den **vilken typ av användare** väljer du en typ.
   
   c. I den **användarnamn** textrutan skriver du namnet på Azure AD-användare.
   
   d. I den **e-post** textrutan skriver du e-postadressen för Azure AD-användare.
   
   e. Välj **skicka användarens e-postadress med instruktionen**.
   
   f. Klicka på **lägga till användare**.

     >[!NOTE]
     > När du har skapat användaren skickas ett e-postmeddelande till Azure AD-användare som innehåller en länk för att bekräfta kontot innan det blir aktiv.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mozy Enterprise.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Mozy Enterprise, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Mozy Enterprise**.

    ![Konfigurera enkel inloggning](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mozy Enterprise i åtkomstpanelen, bör du få inloggningssidan i Mozy företagsprogram.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/mozy-enterprise-tutorial/tutorial_general_203.png

