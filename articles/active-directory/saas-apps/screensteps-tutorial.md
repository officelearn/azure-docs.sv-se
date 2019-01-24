---
title: 'Självstudier: Azure Active Directory-integrering med ScreenSteps | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 50e59c9ab04c1f17d55461b0562491143c21e51d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815913"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Självstudier: Azure Active Directory-integrering med ScreenSteps

I den här självstudien får du lära dig hur du integrerar ScreenSteps med Azure Active Directory (AD Azure).

Integrera ScreenSteps med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ScreenSteps.
- Du kan aktivera användarna att automatiskt få loggat in på ScreenSteps (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ScreenSteps, behöver du följande objekt:

- En Azure AD-prenumeration
- En ScreenSteps enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ScreenSteps från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-screensteps-from-the-gallery"></a>Att lägga till ScreenSteps från galleriet
För att konfigurera integrering av ScreenSteps i Azure AD, som du behöver lägga till ScreenSteps från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ScreenSteps från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **ScreenSteps**väljer **ScreenSteps** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ScreenSteps i resultatlistan](./media/screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ScreenSteps baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ScreenSteps är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ScreenSteps upprättas.

I ScreenSteps, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ScreenSteps, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare ScreenSteps](#create-a-screensteps-test-user)**  – du har en motsvarighet för Britta Simon i ScreenSteps som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ScreenSteps program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ScreenSteps:**

1. I Azure-portalen på den **ScreenSteps** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/screensteps-tutorial/tutorial_screensteps_samlbase.png)

1. På den **ScreenSteps domän och URL: er** avsnittet, utför följande steg:

    ![ScreenSteps domän och URL: er med enkel inloggning för information](./media/screensteps-tutorial/tutorial_screensteps_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL, vilket beskrivs senare i den här självstudien. 

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/screensteps-tutorial/tutorial_screensteps_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/screensteps-tutorial/tutorial_general_400.png)

1. På den **ScreenSteps Configuration** klickar du på **konfigurera ScreenSteps** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![ScreenSteps konfiguration](./media/screensteps-tutorial/tutorial_screensteps_configure.png) 

1. Logga in på webbplatsen ScreenSteps företag som en administratör i ett annat webbläsarfönster.

1. Klicka på **kontoinställningar**.

    ![Kontohantering](./media/screensteps-tutorial/ic778523.png "kontohantering")

1. Klicka på **Enkel inloggning**.

    ![Fjärrautentiseringen](./media/screensteps-tutorial/ic778524.png "fjärrautentiseringen")

1. Klicka på **skapa slutpunkt för enkel inloggning**.

    ![Fjärrautentiseringen](./media/screensteps-tutorial/ic778525.png "fjärrautentiseringen")

1. I den **slutpunkt för skapa enkel inloggning** avsnittet, utför följande steg:

    ![Skapa en autentiseringsslutpunkt](./media/screensteps-tutorial/ic778526.png "skapa en autentiseringsslutpunkt")
    
    a. I den **rubrik** textrutan anger du ett rubrik.
    
    b. Från den **läge** väljer **SAML**.
    
    c. Klicka på **Skapa**.

1. **Redigera** den nya slutpunkten.

    ![Redigera slutpunkten](./media/screensteps-tutorial/ic778528.png "redigera slutpunkten")

1. I den **slutpunkt för Redigera enkel inloggning** avsnittet, utför följande steg:

    ![Remote autentiseringsslutpunkt](./media/screensteps-tutorial/ic778527.png "Remote autentiseringsslutpunkt")

    a. Klicka på **överför nya SAML-certifikatet fil**, och sedan ladda upp certifikatet som du har hämtat från Azure-portalen.
    
    b. Klistra in **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen till den **Remote inloggnings-URL** textrutan.
    
    c. Klistra in **URL: en för utloggning** värde, som du har kopierat från Azure-portalen till den **URL för utloggning** textrutan.
    
    d. Välj en **grupp** att tilldela användare till när de har etablerats.
    
    e. Klicka på **Uppdatera**.

    f. Kopiera den **URL för SAML-konsument** till Urklipp och klistra in till den **inloggnings-URL** -textrutan i **ScreenSteps domän och URL: er** avsnittet.
    
    g. Gå tillbaka till den **redigera slutpunkt för enkel inloggning**.
    
    h. Klicka på den **ange som standard för kontot** knappen för att använda den här slutpunkten för alla användare som loggar in på ScreenSteps. Du kan också klicka på den **lägga till sidan** knappen för att använda den här slutpunkten för specifika platser i **ScreenSteps**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/screensteps-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/screensteps-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/screensteps-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/screensteps-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-screensteps-test-user"></a>Skapa en ScreenSteps testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i ScreenSteps. Arbeta med [ScreenSteps klienten supportteamet](https://www.screensteps.com/contact) att lägga till användare i ScreenSteps-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ScreenSteps.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon ScreenSteps, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **ScreenSteps**.

    ![Länken ScreenSteps i listan med program](./media/screensteps-tutorial/tutorial_screensteps_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ScreenSteps i åtkomstpanelen du bör få automatiskt loggat in på ditt ScreenSteps program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/screensteps-tutorial/tutorial_general_01.png
[2]: ./media/screensteps-tutorial/tutorial_general_02.png
[3]: ./media/screensteps-tutorial/tutorial_general_03.png
[4]: ./media/screensteps-tutorial/tutorial_general_04.png

[100]: ./media/screensteps-tutorial/tutorial_general_100.png

[200]: ./media/screensteps-tutorial/tutorial_general_200.png
[201]: ./media/screensteps-tutorial/tutorial_general_201.png
[202]: ./media/screensteps-tutorial/tutorial_general_202.png
[203]: ./media/screensteps-tutorial/tutorial_general_203.png

