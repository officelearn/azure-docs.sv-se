---
title: 'Självstudier: Azure Active Directory-integrering med Perception USA (icke-UltiPro) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Perception USA (icke-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: f4d01495e48d440c09ad62cd69dbf9b11b7a4cc2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812411"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Självstudier: Azure Active Directory-integrering med Perception USA (icke-UltiPro)

I den här självstudien får du lära dig hur du integrerar Perception USA (icke-UltiPro) med Azure Active Directory (AD Azure).

Integrera Perception USA (icke-UltiPro) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Perception USA (icke-UltiPro).
- Du kan aktivera användarna att automatiskt få loggat in till Perception USA (icke-UltiPro) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Perception USA (icke-UltiPro), behöver du följande objekt:

- En Azure AD-prenumeration
- En Perception USA (icke-UltiPro) enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Perception USA (icke-UltiPro) från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Att lägga till Perception USA (icke-UltiPro) från galleriet
För att konfigurera integrering av Perception USA (icke-UltiPro) till Azure AD, måste du lägga till Perception USA (icke-UltiPro) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Perception USA (icke-UltiPro) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Perception USA (icke-UltiPro)** väljer **Perception USA (icke-UltiPro)** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![Perception USA (icke-UltiPro) i resultatlistan](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Perception USA (icke-UltiPro) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Perception USA (icke-UltiPro) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Perception USA (icke-UltiPro) upprättas.

I Perception USA (icke-UltiPro), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Perception USA (icke-UltiPro), måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Perception USA (icke-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**  – du har en motsvarighet för Britta Simon i Perception USA (icke-UltiPro) som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Perception USA (icke-UltiPro).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Perception USA (icke-UltiPro):**

1. I Azure-portalen på den **Perception USA (icke-UltiPro)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

1. På den **Perception USA (icke-UltiPro)-domän och URL: er** avsnittet, utför följande steg:

    ![Perception USA (icke-UltiPro)-domän och URL: er med enkel inloggning för information](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. I den **identifierare** textrutan anger du URL: `https://perception.kanjoya.com/sp`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Värdet är inte verkligt. Du uppdaterar värdet med den faktiska svars-URL:en, som förklaras senare i självstudiekursen.
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

1. På den **Perception USA (icke-UltiPro) konfiguration** klickar du på **konfigurera Perception USA (icke-UltiPro)** att öppna **konfigurera inloggning** fönster . Kopiera den **SAML entitets-ID** från den **Snabbreferens avsnittet.**

    a. Den **Perception USA (icke-UltiPro)** programmet kräver den **SAML entitets-ID** värde, som du har kopierat kan vara uri-kodas. Om du vill få uri-kodas, använder du följande länk:**http://www.url-encode-decode.com/**.

    b. När du har fått URI: n kodad värde kombinera den med den **svars-URL** som nämns nedan –

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Klistra in ovanstående värde i den **svars-URL** -textrutan i **Perception USA (icke-UltiPro)-domän och URL: er** avsnittet.

    ![Perception USA (icke-UltiPro) konfiguration](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

1. I ett nytt webbläsarfönster inloggning till webbplatsen för företagets Perception USA (icke-UltiPro) som administratör.

1. I verktygsfältet klickar du på **kontoinställningar**.

    ![Perception USA (icke-UltiPro)-användare](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

1. På den **kontoinställningar** utför följande steg:

    ![Perception USA (icke-UltiPro)-användare](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. I den **företagsnamn** textrutan skriver du namnet på den **företagets**.
    
    b. I den **kontonamn** textrutan skriver du namnet på den **konto**.

    c. I **standard svara till e-post** text skriver giltiga **e-post**.

    d. Välj **SSO identitetsprovider** som **SAML 2.0**.

1. På den **SSO-konfiguration** utför följande steg:

    ![Perception USA (icke-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Välj **SAML NameID typ** som **e-post**.

    b. I den **SSO-Konfigurationsnamnet** textrutan skriver du namnet på din **Configuration**.
    
    c. I **namn på identitetsprovider** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen. 

    d. I **SAML domän textrutan**, anger du domänen som **@contoso.com**.

    e. Klicka på **ladda upp igen** att ladda upp den **XML-Metadata för** fil.

    f. Klicka på **Uppdatera**.


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Skapa en testanvändare Perception USA (icke-UltiPro)

I det här avsnittet skapar du en användare som kallas Britta Simon Perception United States (icke-UltiPro). Arbeta med [Perception USA (icke-UltiPro) supportteamet](https://www.ultimatesoftware.com/Contact/ContactUs) att lägga till användare i USA för Perception (icke-UltiPro)-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Perception USA (icke-UltiPro).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Perception USA (icke-UltiPro), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Perception USA (icke-UltiPro)**.

    ![Länken Perception USA (icke-UltiPro) i listan med program](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Perception USA (icke-UltiPro) i åtkomstpanelen du bör få automatiskt loggat in på programmets Perception USA (icke-UltiPro).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png

