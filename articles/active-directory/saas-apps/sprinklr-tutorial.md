---
title: 'Självstudier: Azure Active Directory-integrering med Sprinklr | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1c3b95686b8c91552615a9014102fd6a14f8c385
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277076"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Självstudier: Azure Active Directory-integrering med Sprinklr

I den här självstudien får du lära dig hur du integrerar Sprinklr med Azure Active Directory (AD Azure).
Integrera Sprinklr med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Sprinklr.
* Du kan aktivera användarna att vara automatiskt inloggad till Sprinklr (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Sprinklr, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Sprinklr enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Sprinklr **SP** -initierad SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Att lägga till Sprinklr från galleriet

För att konfigurera integrering av Sprinklr i Azure AD, som du behöver lägga till Sprinklr från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Sprinklr från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Sprinklr**väljer **Sprinklr** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Sprinklr i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Sprinklr baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Sprinklr upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Sprinklr, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Sprinklr Single Sign-On](#configure-sprinklr-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Sprinklr](#create-sprinklr-test-user)**  – du har en motsvarighet för Britta Simon i Sprinklr som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Sprinklr:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Sprinklr** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Sprinklr domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.sprinklr.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Sprinklr klienten supportteamet](https://www.sprinklr.com/contact-us/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Sprinklr** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurera Sprinklr Single Sign-On

1. I ett annat webbläsarfönster logga du in på webbplatsen Sprinklr företag som administratör.

1. Gå till **Administration \> inställningar**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **hantera Partner \> enkelinloggning** på den vänstra rutan.

    ![Hantera Partner](./media/sprinklr-tutorial/ic782908.png "hantera Partner")

1. Klicka på **+ Lägg till enkel inloggning Ons**.

    ![Enkel inloggningar](./media/sprinklr-tutorial/ic782909.png "enkel inloggningar")

1. På den **för enkelinloggning** utför följande steg:

    ![Enkel inloggningar](./media/sprinklr-tutorial/ic782910.png "enkel inloggningar")

    a. I textrutan **Name** (Namn) skriver du ett namn för organisationen (till exempel: *WAADSSOTest*).

    b. Välj **aktiverat**.

    c. Välj **använder nya SSO-certifikat**.

    d. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **Identity Provider Certificate** (Certifikat för identitetsprovider).

    e. Klistra in den **Azure AD-identifierare** värde som du har kopierat från Azure-portalen till den **entitets-Id** textrutan.

    f. Klistra in den **inloggnings-URL** värde som du har kopierat från Azure-portalen till den **inloggnings-URL för identitetsprovider** textrutan.

    g. Klistra in den **URL för utloggning** värde som du har kopierat från Azure-portalen till den **utloggnings-URL för identitetsprovider** textrutan.

    h. Som **SAML-ID användartyp**väljer **Assertion innehåller användarens sprinklr.com användarnamn**.

    i. Som **SAML-ID Användarplats**väljer **användar-ID är i namnidentifierare elementet i instruktionen ämne**.

    j. Klicka på **Spara**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sprinklr.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Sprinklr**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Sprinklr**.

    ![Länken Sprinklr i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sprinklr-test-user"></a>Skapa Sprinklr testanvändare

1. Logga in på webbplatsen Sprinklr företag som administratör.

1. Gå till **Administration \> inställningar**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **hantera klienten \> användare** i den vänstra rutan.

    ![Inställningar](./media/sprinklr-tutorial/ic782914.png "Inställningar")

1. Klicka på **Lägg till användare**.

    ![Inställningar](./media/sprinklr-tutorial/ic782915.png "Inställningar")

1. På den **Redigera användare** dialogrutan utför följande steg:

    ![Redigera användare](./media/sprinklr-tutorial/ic782916.png "Redigera användare")

    a. I den **e-post**, **Förnamn** och **efternamn** textrutor, skriver du informationen för en Azure AD-användarkonto som du vill etablera.

    b. Välj **lösenord inaktiverad**.

    c. Välj **språk**.

    d. Välj **användartyp**.

    e. Klicka på **Uppdatera**.

    > [!IMPORTANT]
    > **Lösenord inaktiveras** måste väljas för att aktivera en användare att logga in via en identitetsleverantör. 

1. Gå till **rollen**, och utför följande steg:

    ![Partner roller](./media/sprinklr-tutorial/ic782917.png "Partner roller")

    a. Från den **Global** väljer **ALL_Permissions**.  

    b. Klicka på **Uppdatera**.

> [!NOTE]
> Du kan använda alla andra Sprinklr användare konto verktyg för att skapa eller API: er som tillhandahålls av Sprinklr att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Sprinklr i åtkomstpanelen, bör det vara loggas in automatiskt till Sprinklr som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
