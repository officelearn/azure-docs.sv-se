---
title: 'Självstudier: Azure Active Directory-integrering med bröstknappar SAML SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och bröstknappar SAML SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 828c981c-c3dd-4eb2-8699-0f732baa43f6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cb0822380fd375c111f8acb8b28132727a7c048
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188315"
---
# <a name="tutorial-azure-active-directory-integration-with-insignia-saml-sso"></a>Självstudier: Azure Active Directory-integrering med bröstknappar SAML SSO

I den här självstudien får du lära dig hur du integrerar bröstknappar SAML SSO med Azure Active Directory (AD Azure).

Integrera bröstknappar SAML SSO med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till bröstknappar SAML SSO.
- Du kan aktivera användarna att automatiskt få loggat in på bröstknappar SAML SSO (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med bröstknappar SAML SSO, behöver du följande objekt:

- En Azure AD-prenumeration
- En bröstknappar SAML SSO enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till bröstknappar SAML SSO från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-insignia-saml-sso-from-the-gallery"></a>Att lägga till bröstknappar SAML SSO från galleriet
Om du vill konfigurera integreringen av bröstknappar SAML SSO till Azure AD, som du behöver lägga till bröstknappar SAML SSO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till bröstknappar SAML SSO från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **bröstknappar SAML SSO**väljer **bröstknappar SAML SSO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Bröstknappar SAML SSO i resultatlistan](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med bröstknappar SAML SSO baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i bröstknappar SAML SSO är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i bröstknappar SAML SSO upprättas.

I bröstknappar SAML SSO, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med bröstknappar SAML SSO, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare bröstknappar SAML SSO](#create-an-insignia-saml-sso-test-user)**  – du har en motsvarighet för Britta Simon i bröstknappar SAML SSO som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt bröstknappar SAML SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med bröstknappar SAML SSO:**

1. I Azure-portalen på den **bröstknappar SAML SSO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_samlbase.png)

1. På den **bröstknappar SAML SSO-domän och URL: er** avsnittet, utför följande steg:

    ![Bröstknappar SAML SSO-domän och URL: er med enkel inloggning för information](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
    | |
    |--|
    | `https://<customername>.insigniails.com/ils` |
    | `https://<customername>.insigniails.com/` |
    | `https://<customername>.insigniailsusa.com/ ` |

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<customername>.insigniailsusa.com/<uniqueid>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [bröstknappar SAML SSO-klienten supportteamet](https://www.insigniasoftware.com/insignia/Techsupport.aspx) att hämta dessa värden. 
 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/insigniasamlsso-tutorial/tutorial_general_400.png)

1. På den **bröstknappar SAML SSO-konfiguration** klickar du på **konfigurera bröstknappar SAML SSO** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Bröstknappar SAML SSO-konfiguration](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_configure.png) 

1. Att konfigurera enkel inloggning på **bröstknappar SAML SSO** sida, som du behöver skicka de hämtade **certifikat (Base64)**, **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** till [Bröstknappar SAML SSO-supportteamet](https://www.insigniasoftware.com/insignia/Techsupport.aspx). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/insigniasamlsso-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/insigniasamlsso-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/insigniasamlsso-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/insigniasamlsso-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-insignia-saml-sso-test-user"></a>Skapa en testanvändare bröstknappar SAML SSO

I det här avsnittet skapar du en användare som kallas Britta Simon i bröstknappar biblioteket System. Arbeta med [bröstknappar biblioteket System supportteamet](https://www.insigniasoftware.com/insignia/Techsupport.aspx) att lägga till användare i bröstknappar biblioteket System-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till bröstknappar SAML SSO.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon bröstknappar SAML SSO, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **bröstknappar SAML SSO**.

    ![Bröstknappar SAML SSO-länk i listan med program](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen bröstknappar SAML SSO i åtkomstpanelen du bör få automatiskt loggat in på ditt bröstknappar SAML SSO-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/insigniasamlsso-tutorial/tutorial_general_01.png
[2]: ./media/insigniasamlsso-tutorial/tutorial_general_02.png
[3]: ./media/insigniasamlsso-tutorial/tutorial_general_03.png
[4]: ./media/insigniasamlsso-tutorial/tutorial_general_04.png

[100]: ./media/insigniasamlsso-tutorial/tutorial_general_100.png

[200]: ./media/insigniasamlsso-tutorial/tutorial_general_200.png
[201]: ./media/insigniasamlsso-tutorial/tutorial_general_201.png
[202]: ./media/insigniasamlsso-tutorial/tutorial_general_202.png
[203]: ./media/insigniasamlsso-tutorial/tutorial_general_203.png

