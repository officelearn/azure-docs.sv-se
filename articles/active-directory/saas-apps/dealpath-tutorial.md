---
title: 'Självstudier: Azure Active Directory-integration med Dealpath | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dealpath.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 51ace608-5a4f-48c0-9446-d9f86ad2e890
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: jeedes
ms.openlocfilehash: c79e9352cbeb1cf6349460d4e0b1b61c351f0c40
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053435"
---
# <a name="tutorial-azure-active-directory-integration-with-dealpath"></a>Självstudier: Azure Active Directory-integration med Dealpath

I den här självstudien får du lära dig hur du integrerar Dealpath med Azure Active Directory (AD Azure).

Integrera Dealpath med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Dealpath.
- Du kan aktivera användarna att automatiskt få loggat in på Dealpath (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Dealpath, behöver du följande objekt:

- En Azure AD-prenumeration
- En Dealpath enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Dealpath från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dealpath-from-the-gallery"></a>Att lägga till Dealpath från galleriet
För att konfigurera integrering av Dealpath i Azure AD, som du behöver lägga till Dealpath från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Dealpath från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Dealpath**väljer **Dealpath** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Dealpath i resultatlistan](./media/dealpath-tutorial/tutorial_dealpath_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Dealpath baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Dealpath är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Dealpath upprättas.

I Dealpath, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Dealpath, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Dealpath](#create-a-dealpath-test-user)**  – du har en motsvarighet för Britta Simon i Dealpath som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Dealpath program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Dealpath:**

1. I Azure-portalen på den **Dealpath** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/dealpath-tutorial/tutorial_dealpath_samlbase.png)

3. På den **Dealpath domän och URL: er** avsnittet, utför följande steg:

    ![Dealpath domän och URL: er med enkel inloggning för information](./media/dealpath-tutorial/tutorial_dealpath_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://app.dealpath.com/account/login`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://api.dealpath.com/saml/metadata/<ID>`

    > [!NOTE] 
    > Identifieraren är inte verkliga. Uppdatera värdet med det faktiska ID: T. Kontakta [Dealpath klienten supportteamet](mailto:kenter@dealpath.com) att hämta värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/dealpath-tutorial/tutorial_dealpath_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/dealpath-tutorial/tutorial_general_400.png)

6. På den **Dealpath Configuration** klickar du på **konfigurera Dealpath** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Dealpath konfiguration](./media/dealpath-tutorial/tutorial_dealpath_configure.png) 

7. I ett annat webbläsarfönster, logga in på Dealpath som administratör.

8. Uppe till höger, klickar du på **Administrationsverktyg** och gå till **integreringar**, i **SAML 2.0-autentisering** klickar du på avsnittet **uppdateringsinställningar**:

    ![Dealpath konfiguration](./media/dealpath-tutorial/tutorial_dealpath_admin.png)

9. I den **konfigurera SAML 2.0-autentisering** utför följande steg:

    ![Dealpath konfiguration](./media/dealpath-tutorial/tutorial_dealpath_saml.png) 

    a. I den **URL för SAML SSO** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    b. I den **Providerutgivare** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    c. Kopiera innehållet i de hämtade **certificate(Base64)** filen i anteckningar och klistra in den i den **offentligt certifikat** textrutan.

    d. Klicka på **uppdatera inställningarna för**.


> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/dealpath-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/dealpath-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/dealpath-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/dealpath-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-dealpath-test-user"></a>Skapa en Dealpath testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Dealpath. Arbeta med [Dealpath klienten supportteamet](mailto:kenter@dealpath.com) att lägga till användare i Dealpath-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Dealpath.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Dealpath, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Dealpath**.

    ![Länken Dealpath i listan med program](./media/dealpath-tutorial/tutorial_dealpath_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Dealpath i åtkomstpanelen du bör få automatiskt loggat in på ditt Dealpath program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dealpath-tutorial/tutorial_general_01.png
[2]: ./media/dealpath-tutorial/tutorial_general_02.png
[3]: ./media/dealpath-tutorial/tutorial_general_03.png
[4]: ./media/dealpath-tutorial/tutorial_general_04.png

[100]: ./media/dealpath-tutorial/tutorial_general_100.png

[200]: ./media/dealpath-tutorial/tutorial_general_200.png
[201]: ./media/dealpath-tutorial/tutorial_general_201.png
[202]: ./media/dealpath-tutorial/tutorial_general_202.png
[203]: ./media/dealpath-tutorial/tutorial_general_203.png

