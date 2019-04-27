---
title: 'Självstudier: Azure Active Directory-integrering med Voyance | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Voyance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1771fe1d94eb64cf1e5227953bdc5defa488e85f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639366"
---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Självstudier: Azure Active Directory-integrering med Voyance

I den här självstudien får du lära dig hur du integrerar Voyance med Azure Active Directory (AD Azure).

Integrera Voyance med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Voyance
- Du kan aktivera användarna att automatiskt få loggat in på Voyance (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Voyance, behöver du följande objekt:

- En Azure AD-prenumeration
- En Voyance enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Voyance från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-voyance-from-the-gallery"></a>Att lägga till Voyance från galleriet
För att konfigurera integrering av Voyance i Azure AD, som du behöver lägga till Voyance från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Voyance från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Voyance**väljer **Voyance** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Voyance i resultatlistan](./media/voyance-tutorial/tutorial_voyance_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Voyance baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Voyance är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Voyance upprättas.

I Voyance, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Voyance, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Voyance](#create-a-voyance-test-user)**  – du har en motsvarighet för Britta Simon i Voyance som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Voyance program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Voyance:**

1. I Azure-portalen på den **Voyance** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/voyance-tutorial/tutorial_voyance_samlbase.png)

1. På den **Voyance domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Voyance domän och URL: er med enkel inloggning information för IDP: N](./media/voyance-tutorial/tutorial_voyance_url1.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<companyname>.nyansa.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.nyansa.com/saml/create/`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Enkel inloggning information för SP Voyance domän och URL: er](./media/voyance-tutorial/tutorial_voyance_url2.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.nyansa.com/`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Voyance klienten supportteamet](mailto:support@nyansa.com) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/voyance-tutorial/tutorial_voyance_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/voyance-tutorial/tutorial_general_400.png)
    
1. På den **Voyance Configuration** klickar du på **konfigurera Voyance** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Voyance konfiguration](./media/voyance-tutorial/tutorial_voyance_configure.png) 

1. I ett annat webbläsarfönster inloggning till Voyance-klienten som administratör.

1. Gå till det övre högra hörnet i navigeringsfältet och klicka på listrutan med texten ”**Acme University**”.
    
    ![Konfigurera enkel inloggning på App på serversidan Acme University](./media/voyance-tutorial/tutorial_voyance_001.png) 

1. Klicka på ”**administratörsinställningar**”.

    ![Konfigurera enkel inloggning på App-sida administratörsinställningar](./media/voyance-tutorial/tutorial_voyance_002.png)

1. Klicka på ”**användaråtkomst**” fliken.

    ![Konfigurera enkel inloggning på App-sida användaråtkomst](./media/voyance-tutorial/tutorial_voyance_003.png)

1. Klicka på den ”**SSO är inaktiverad**” knappen för att konfigurera Azure AD som en IdP som använder SAML 2.0.

    ![Konfigurera enkel inloggning på App på serversidan SSO inaktiveras knappen](./media/voyance-tutorial/tutorial_voyance_004.png)

1. Gå till **SAML v2** avsnittet och utföra stegen nedan:

    ![Konfigurera enkel inloggning på App-sida SAML v2](./media/voyance-tutorial/tutorial_voyance_005.png)
    
    a. Välj **aktiverat**.
    
    b. Klistra in **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen till den **inloggnings-URL för IDP: N** textrutan.

    c. Öppna din hämtade Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **IdP-Cert** textrutan.
    
    d. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/voyance-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Länkarna ”Användare och grupper” och ”Alla grupper”](./media/voyance-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/voyance-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan Användare](./media/voyance-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-voyance-test-user"></a>Skapa en Voyance testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Voyance. Voyance stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Voyance om det inte finns ännu.

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta [Voyance supportteamet](maiLto:support@nyansa.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Voyance.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Voyance, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Voyance**.

    ![Länken Voyance i listan med program](./media/voyance-tutorial/tutorial_voyance_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Voyance i åtkomstpanelen du bör få automatiskt loggat in på ditt Voyance program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/voyance-tutorial/tutorial_general_01.png
[2]: ./media/voyance-tutorial/tutorial_general_02.png
[3]: ./media/voyance-tutorial/tutorial_general_03.png
[4]: ./media/voyance-tutorial/tutorial_general_04.png

[100]: ./media/voyance-tutorial/tutorial_general_100.png

[200]: ./media/voyance-tutorial/tutorial_general_200.png
[201]: ./media/voyance-tutorial/tutorial_general_201.png
[202]: ./media/voyance-tutorial/tutorial_general_202.png
[203]: ./media/voyance-tutorial/tutorial_general_203.png

