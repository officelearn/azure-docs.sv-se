---
title: 'Självstudier: Azure Active Directory-integrering med Pingboard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pingboard.
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
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 02aabb9260cb0140b61091a8002f70d464091ddd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818123"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Självstudier: Azure Active Directory-integrering med Pingboard

I den här självstudien får du lära dig hur du integrerar Pingboard med Azure Active Directory (AD Azure).

Integrera Pingboard med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Pingboard
- Du kan aktivera användarna att automatiskt få loggat in på Pingboard (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pingboard, behöver du följande objekt:

- En Azure AD-prenumeration
- En Pingboard enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Pingboard från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pingboard-from-the-gallery"></a>Att lägga till Pingboard från galleriet
För att konfigurera integrering av Pingboard i Azure AD, som du behöver lägga till Pingboard från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Pingboard från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Enterprise-program][2]

1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Pingboard**väljer **Pingboard** resultatet panelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Pingboard i resultatlistan](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pingboard baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Pingboard är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Pingboard upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Pingboard.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pingboard, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Pingboard](#create-a-pingboard-test-user)**  – du har en motsvarighet för Britta Simon i Pingboard som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Pingboard program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Pingboard:**

1. I Azure-portalen på den **Pingboard** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1.  På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

1. På den **Pingboard domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Pingboard domän och URL: er med enkel inloggning information IDP](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. I den **identifierare** textrutan Ange värdet som: `http://app.pingboard.com/sp`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<entity-id>.pingboard.com/auth/saml/consume`

1. Kontrollera **visa avancerade URL-inställningar**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Enkel inloggning information SP Pingboard domän och URL: er](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     I den **inloggnings-URL** textrutan anger du URL med hjälp av följande mönster: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Observera att värdena inte är verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Pingboard klienten supportteamet](https://support.pingboard.com/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Xml-metadata för Pingboard](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pingboard-tutorial/tutorial_general_400.png)

1. För att konfigurera enkel inloggning på Pingboard sida, öppna ett nytt webbläsarfönster och logga in på ditt Pingboard-konto. Du måste vara administratör för Pingboard att konfigurera enkel inloggning.

1. På den översta menyn, Välj **appar > integreringar**

    ![Konfigurera enkel inloggning](./media/pingboard-tutorial/Pingboard_integration.png)

1. På den **integreringar** sidan, hitta den **”Azure Active Directory”** panelen och klicka på den.

    ![Pingboard Single-Sign-On-integrering](./media/pingboard-tutorial/Pingboard_aad.png)

1. I modal som följer Klicka **”konfigurera”**

    ![Pingboard configuration knappen](./media/pingboard-tutorial/Pingboard_configure.png)

1. På sidan Observera att ”Azure SSO-integrering är aktiverat”. Öppna den hämta Metadata XML-filen i anteckningar och klistra in innehållet i **IDP Metadata**.

    ![Skärm för konfiguration av Pingboard SSO](./media/pingboard-tutorial/Pingboard_sso_configure.png)

1. Filen har verifierats och om allt är korrekt, enkel inloggning kommer nu att aktiveras.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/pingboard-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](./media/pingboard-tutorial/create_aaduser_02.png)

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.

    ![Knappen Lägg till](./media/pingboard-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan Användare](./media/pingboard-tutorial/create_aaduser_04.png)

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="create-a-pingboard-test-user"></a>Skapa en Pingboard testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pingboard. Pingboard stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information [här](pingboard-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på webbplatsen Pingboard företag som administratör.

1. Klicka på **”Lägg till medarbetare”** knappen **Directory** sidan.

    ![Lägga till medarbetare](./media/pingboard-tutorial/create_testuser_add.png)

1. På den **”Lägg till medarbetare”** dialogrutan utför följande steg:

    ![Bjuda in](./media/pingboard-tutorial/create_testuser_name.png)

    a. I den **fullständigt namn** textrutan typ det fullständiga namnet för användaren som **Britta Simon**.

    b. I den **e-post** textrutan typ e-postadressen för användaren som **brittasimon@contoso.com**.

    c. I den **befattning** textrutan skriver Britta Simon befattning.

    d. I den **plats** listrutan Välj platsen för Britta Simon.

    e. Klicka på **Lägg till**.

1. En bekräftelseskärm visas för att bekräfta att lägga till användaren.

    ![Bekräfta](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory-kontoinnehavare tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pingboard.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Pingboard, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Pingboard**.

    ![Länken Pingboard i listan med program](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

När du klickar på panelen Pingboard i åtkomstpanelen du bör få automatiskt loggat in på ditt Pingboard program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera användarförsörjning](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png