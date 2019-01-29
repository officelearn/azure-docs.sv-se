---
title: 'Självstudier: Azure Active Directory-integrering med Autotask arbetsplats | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Autotask arbetsplats.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: c95310043415dc689149521716f37099bfa084c0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168948"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Självstudier: Azure Active Directory-integrering med Autotask arbetsplats

I den här självstudien får du lära dig hur du integrerar Autotask arbetsplats med Azure Active Directory (AD Azure).

Integrera Autotask arbetsplats med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Autotask arbetsplats
- Du kan aktivera användarna att automatiskt få loggat in på Autotask arbetsplats (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Autotask arbetsplats, behöver du följande objekt:

- En Azure AD-prenumeration
- En Autotask arbetsplats enkel inloggning aktiverad prenumeration
- Du måste vara administratör eller super administratör i arbetsplats.
- Du måste ha ett administratörskonto i Azure AD.
- De användare som ska använda den här funktionen måste ha konton inom arbetsplats och Azure AD och deras e-postadresser för både måste matcha.

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Autotask arbetsplats från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-autotask-workplace-from-the-gallery"></a>Att lägga till Autotask arbetsplats från galleriet
För att konfigurera integrering av Autotask arbetsplats i Azure AD, som du behöver lägga till Autotask arbetsplats från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Autotask arbetsplats från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Autotask arbetsplats**väljer **Autotask arbetsplats** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Autotask arbetsplats i resultatlistan](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Autotask arbetsplats baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Autotask arbetsplats är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Autotask arbetsplatsen upprättas.

I Autotask arbetsplats, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Autotask arbetsplats, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Autotask arbetsplats](#create-an-autotask-workplace-test-user)**  – du har en motsvarighet för Britta Simon i Autotask arbetsplats som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Autotask arbetsplats.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Autotask arbetsplats:**

1. I Azure-portalen på den **Autotask arbetsplats** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

1. Om du vill konfigurera programmet i **IDP** initierade läge, utför följande steg på den **Autotask arbetsplats domän och URL: er** avsnittet:

    ![Autotask arbetsplats domän och URL: er enkel inloggning information för IDP: N](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

1. Om du vill konfigurera programmet i **SP** initierad läge, kontrollera **visa avancerade URL-inställningar** och utför följande steg:

    ![Autotask arbetsplats domän och URL: er enkel inloggning information för SP](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.awp.autotask.net/loginsso`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Autotask arbetsplats klienten supportteamet](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/autotaskworkplace-tutorial/tutorial_general_400.png)

1. Logga in på arbetsplatsen Online med administratörsautentiseringsuppgifterna som i ett annat webbläsarfönster.

    >[!Note]
    >När du konfigurerar IDP: N, måste en underdomän anges. Att fastställa rätt underdomänen, logga in på arbetsplatsen Online. När du loggat in kan du anteckna underdomänen i URL-Adressen visas.
    >Underdomänen ingår mellan ”https://” och ”.awp.autotask.net/” och ska vara oss, eu, Kanada eller Australien.

1. Gå till **Configuration** > **enkel inloggning** och utför följande steg:

    ![Konfiguration för Autotask enkel inloggning](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. Välj den **XML-metadatafil** alternativet och överför sedan den **XML-Metadata för** hämtas från Azure-portalen.

    b. Klicka på **aktivera enkel inloggning**.
    
    ![Godkänn Autotask Single Sign-on konfiguration](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Välj den **jag bekräftar att informationen är korrekt och jag litar på den här IdP** markerar du kryssrutan.

    d. Klicka på **godkänna**.
     
>[!Note]
>Om du behöver hjälp med att konfigurera Autotask arbetsplats finns [den här sidan](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) att få hjälp med ditt arbetsplats-konto.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/autotaskworkplace-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/autotaskworkplace-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/autotaskworkplace-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/autotaskworkplace-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-an-autotask-workplace-test-user"></a>Skapa en testanvändare Autotask arbetsplats

I det här avsnittet skapar du en användare som kallas Britta Simon i Autotask. Kontakta [Autotask arbetsplats supportteamet](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) att lägga till användare i Autotask arbetsplats-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Autotask arbetsplats.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Autotask arbetsplats, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Autotask arbetsplats**.

    ![Länken Autotask arbetsplats i listan med program](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Autotask arbetsplats i åtkomstpanelen du bör få automatiskt loggat in på programmets Autotask arbetsplats.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/autotaskworkplace-tutorial/tutorial_general_203.png

