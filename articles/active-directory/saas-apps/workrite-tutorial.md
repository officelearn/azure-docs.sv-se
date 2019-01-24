---
title: 'Självstudier: Azure Active Directory-integrering med Workrite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workrite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2a5c2956-a011-4d5c-877b-80679b6587b5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 089f642e015646768bc1e2eca6a04a3b9b7fc8be
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824923"
---
# <a name="tutorial-azure-active-directory-integration-with-workrite"></a>Självstudier: Azure Active Directory-integrering med Workrite

I den här självstudien får du lära dig hur du integrerar Workrite med Azure Active Directory (AD Azure).

Integrera Workrite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Workrite.
- Du kan aktivera användarna att automatiskt få loggat in på Workrite (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Workrite, behöver du följande objekt:

- En Azure AD-prenumeration
- En Workrite enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Workrite från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workrite-from-the-gallery"></a>Att lägga till Workrite från galleriet
För att konfigurera integrering av Workrite i Azure AD, som du behöver lägga till Workrite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Workrite från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Workrite**väljer **Workrite** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Workrite i resultatlistan](./media/workrite-tutorial/tutorial_workrite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Workrite baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Workrite är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Workrite upprättas.

I Workrite, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workrite, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Workrite](#create-a-workrite-test-user)**  – du har en motsvarighet för Britta Simon i Workrite som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Workrite program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Workrite:**

1. I Azure-portalen på den **Workrite** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/workrite-tutorial/tutorial_workrite_samlbase.png)

1. På den **Workrite domän och URL: er** avsnittet, utför följande steg:

    ![Workrite domän och URL: er med enkel inloggning för information](./media/workrite-tutorial/tutorial_workrite_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=<uniqueid>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Workrite klienten supportteamet](mailto:support@workrite.co.uk) att hämta det här värdet.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/workrite-tutorial/tutorial_workrite_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/workrite-tutorial/tutorial_general_400.png)

1. På den **Workrite Configuration** klickar du på **konfigurera Workrite** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Workrite konfiguration](./media/workrite-tutorial/tutorial_workrite_configure.png) 

1. Att konfigurera enkel inloggning på **Workrite** sida, som du behöver skicka de hämtade **Certificate(Base64), URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Workrite supportteam](mailto:support@workrite.co.uk).

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/workrite-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/workrite-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/workrite-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/workrite-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-workrite-test-user"></a>Skapa en Workrite testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Workrite.

**Om du vill skapa en användare som kallas Britta Simon i Workrite, utför du följande steg:**

1. Logga in på webbplatsen workrite företag som administratör.

1. I navigeringsfönstret klickar du på **Admin**.
   
    ![Administratörskontroll][400]

1. Gå till snabblänkar och klicka sedan på **skapa en användare**.
   
    ![Skapa användare][401]

1. På den **Create User** dialogrutan utför följande steg:
   
    ![Skapa användare Dailog][402]
    
    a. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    b. I den **Förnamn** textrutan skriver firstname för användare som Britta.

    c. I den **efternamn** textrutan skriver användarens som Simon efternamn.
    
    d. Välj **Klientadministratör** som **väljer du rollen**.
    
    e. Klicka på **Spara**.   

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workrite.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Workrite, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Workrite**.

    ![Länken Workrite i listan med program](./media/workrite-tutorial/tutorial_workrite_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Workrite i åtkomstpanelen du bör få automatiskt loggat in på ditt Workrite program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/workrite-tutorial/tutorial_general_01.png
[2]: ./media/workrite-tutorial/tutorial_general_02.png
[3]: ./media/workrite-tutorial/tutorial_general_03.png
[4]: ./media/workrite-tutorial/tutorial_general_04.png

[100]: ./media/workrite-tutorial/tutorial_general_100.png

[200]: ./media/workrite-tutorial/tutorial_general_200.png
[201]: ./media/workrite-tutorial/tutorial_general_201.png
[202]: ./media/workrite-tutorial/tutorial_general_202.png
[203]: ./media/workrite-tutorial/tutorial_general_203.png
[400]: ./media/workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/workrite-tutorial/tutorial_workrite_402.png

