---
title: 'Självstudier: Azure Active Directory-integrering med Evernote | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 8ba80e113de8ea6754d8d2d6446fb26498904e12
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816814"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Självstudier: Azure Active Directory-integrering med Evernote

I den här självstudien får du lära dig hur du integrerar Evernote med Azure Active Directory (AD Azure).

Integrera Evernote med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Evernote.
- Du kan aktivera användarna att automatiskt få loggat in till Evernote (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Evernote, behöver du följande objekt:

- En Azure AD-prenumeration
- En Evernote enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Evernote från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-evernote-from-the-gallery"></a>Att lägga till Evernote från galleriet
För att konfigurera integrering av Evernote i Azure AD, som du behöver lägga till Evernote från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Evernote från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Evernote**väljer **Evernote** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Evernote i resultatlistan](./media/evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Evernote baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Evernote är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Evernote upprättas.

I Evernote, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Evernote, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Evernote](#create-an-evernote-test-user)**  – du har en motsvarighet för Britta Simon i Evernote som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Evernote program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Evernote:**

1. I Azure-portalen på den **Evernote** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/evernote-tutorial/tutorial_evernote_samlbase.png)

1. På den **Evernote domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Evernote domän och URL: er med enkel inloggning för information](./media/evernote-tutorial/tutorial_evernote_url.png)

    I den **identifierare** textrutan anger du URL: `https://www.evernote.com/saml2`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Evernote domän och URL: er med enkel inloggning för information](./media/evernote-tutorial/tutorial_evernote_url1.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://www.evernote.com/Login.action`   

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/evernote-tutorial/tutorial_evernote_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/evernote-tutorial/tutorial_general_400.png)

1. På den **Evernote Configuration** klickar du på **konfigurera Evernote** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Evernote konfiguration](./media/evernote-tutorial/tutorial_evernote_configure.png) 

1. Logga in på webbplatsen Evernote företag som en administratör i ett annat webbläsarfönster.

1. Gå till **Admin-konsolen**

    ![-Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Från den **Admin-konsolen**går du till **”säkerhet”** och välj **”enkel inloggning”**

    ![SSO-Setting](./media/evernote-tutorial/tutorial_evernote_sso.png)

1. Konfigurera följande värden:

    ![Certifikat-inställning](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Aktivera enkel inloggning:** Enkel inloggning är aktiverat som standard (klicka på **inaktivera enkel inloggning** att ta bort kravet på enkel inloggning)

    b. Klistra in **SAML enkel inloggning för tjänst-URL för** värde, som du har kopierat från Azure-portalen till den **SAML HTTP fråge-URL** textrutan.

    c. Öppna det nedladdade certifikatet från Azure AD i anteckningar och kopiera innehållet, inklusive ”BÖRJA CERTIFICATE” och ”END CERTIFICATE” och klistra in den i den **X.509-certifikat** textrutan. 

    d.Click **spara ändringar**

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/evernote-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/evernote-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/evernote-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/evernote-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-evernote-test-user"></a>Skapa en testanvändare Evernote

För att aktivera Azure AD-användare att logga in på Evernote, måste de etableras till Evernote.  
När det gäller Evernote är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Evernote företag som administratör.

1. Klicka på den **Admin-konsolen**.

    ![-Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Från den **Admin-konsolen**går du till **”Lägg till användare”**.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

1. **Lägg till gruppmedlemmar** i den **e-post** textrutan skriver du e-postadressen till användarkontot och klicka på **Bjud in.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
1. När inbjudan skickas får den Azure Active Directory-kontoinnehavaren ett e-postmeddelande för att tacka ja till inbjudan.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Evernote.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Evernote, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Evernote**.

    ![Länken Evernote i listan med program](./media/evernote-tutorial/tutorial_evernote_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Evernote i åtkomstpanelen du bör få loggat in på ditt Evernote program. Du måste logga in som en organisation som konto men måste du logga in med ditt personliga konto. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/evernote-tutorial/tutorial_general_01.png
[2]: ./media/evernote-tutorial/tutorial_general_02.png
[3]: ./media/evernote-tutorial/tutorial_general_03.png
[4]: ./media/evernote-tutorial/tutorial_general_04.png

[100]: ./media/evernote-tutorial/tutorial_general_100.png

[200]: ./media/evernote-tutorial/tutorial_general_200.png
[201]: ./media/evernote-tutorial/tutorial_general_201.png
[202]: ./media/evernote-tutorial/tutorial_general_202.png
[203]: ./media/evernote-tutorial/tutorial_general_203.png

