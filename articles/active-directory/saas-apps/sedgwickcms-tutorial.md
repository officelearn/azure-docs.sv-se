---
title: 'Självstudier: Azure Active Directory-integrering med Sedgwick CMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sedgwick CMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 957931e0-e426-47e7-9904-3ed98d3f504c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7783da2e3e5fe2ee79942b246e452ad7998e278
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193330"
---
# <a name="tutorial-azure-active-directory-integration-with-sedgwick-cms"></a>Självstudier: Azure Active Directory-integrering med Sedgwick CMS

I den här självstudien får du lära dig hur du integrerar Sedgwick CMS med Azure Active Directory (AD Azure).

Integrera Sedgwick CMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Sedgwick CMS.
- Du kan aktivera användarna att automatiskt få loggat in på Sedgwick CMS (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Sedgwick CMS, behöver du följande objekt:

- En Azure AD-prenumeration
- En Sedgwick CMS enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Sedgwick CMS från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sedgwick-cms-from-the-gallery"></a>Att lägga till Sedgwick CMS från galleriet
För att konfigurera integrering av Sedgwick CMS i Azure AD, som du behöver lägga till Sedgwick CMS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Sedgwick CMS från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Sedgwick CMS**väljer **Sedgwick CMS** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Sedgwick CMS i resultatlistan](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Sedgwick CMS utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Sedgwick CMS är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Sedgwick CMS upprättas.

I Sedgwick CMS-system, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Sedgwick CMS, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Sedgwick CMS](#create-a-sedgwick-cms-test-user)**  – du har en motsvarighet för Britta Simon i Sedgwick CMS som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Sedgwick CMS-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Sedgwick CMS:**

1. I Azure-portalen på den **Sedgwick CMS** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_samlbase.png)

1. På den **Sedgwick CMS domän och URL: er** avsnittet, utför följande steg:

    ![Sedgwick CMS domän och URL: er med enkel inloggning för information](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_url.png)

    a. I textrutan **Identifierare** anger du URL:en: 
    | |
    |--|
    | `expresspreview.sedgwickcms.net/voe/sso` |
    | `claimlookup.com/Voe/sso` |

    b. I textrutan **Svars-URL** anger du en URL med följande mönster:
    | |
    |--|
    | `https://<subdomain>.sedgwickcms.net/voe/sso` |
    | `https://claimlookup.com/Voe/sso` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Sedgwick CMS-supportteamet](https://www.sedgwick.com/help) att hämta dessa värden.
 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/sedgwickcms-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Sedgwick CMS** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Sedgwick CMS-supportteamet](https://www.sedgwick.com/help). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/sedgwickcms-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/sedgwickcms-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/sedgwickcms-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/sedgwickcms-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-sedgwick-cms-test-user"></a>Skapa en testanvändare Sedgwick CMS

I det här avsnittet skapar du en användare som kallas Britta Simon i Sedgwick CMS. Arbeta med [Sedgwick CMS-supportteamet](https://www.sedgwick.com/help) att lägga till användare i Sedgwick CMS-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sedgwick CMS.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Sedgwick CMS, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Sedgwick CMS**.

    ![Sedgwick CMS-länk i listan med program](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Sedgwick CMS på åtkomstpanelen, du bör få automatiskt loggat in på ditt Sedgwick CMS-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sedgwickcms-tutorial/tutorial_general_01.png
[2]: ./media/sedgwickcms-tutorial/tutorial_general_02.png
[3]: ./media/sedgwickcms-tutorial/tutorial_general_03.png
[4]: ./media/sedgwickcms-tutorial/tutorial_general_04.png

[100]: ./media/sedgwickcms-tutorial/tutorial_general_100.png

[200]: ./media/sedgwickcms-tutorial/tutorial_general_200.png
[201]: ./media/sedgwickcms-tutorial/tutorial_general_201.png
[202]: ./media/sedgwickcms-tutorial/tutorial_general_202.png
[203]: ./media/sedgwickcms-tutorial/tutorial_general_203.png

