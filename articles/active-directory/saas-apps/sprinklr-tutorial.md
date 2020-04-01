---
title: 'Självstudiekurs: Azure Active Directory-integrering med Sprinklr | Microsoft-dokument'
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
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089650"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Självstudiekurs: Azure Active Directory-integrering med Sprinklr

I den här självstudien får du lära dig hur du integrerar Sprinklr med Azure Active Directory (Azure AD).
Genom att integrera Sprinklr med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Sprinklr.
* Du kan aktivera dina användare så att de automatiskt loggas in på Sprinklr (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Sprinklr behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Sprinklr enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Sprinklr stöder **SP** initierade SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Lägga till Sprinklr från galleriet

Om du vill konfigurera integreringen av Sprinklr i Azure AD måste du lägga till Sprinklr från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Sprinklr från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Sprinklr**i sökrutan och välj **Sprinklr** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Sprinklr i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Sprinklr baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Sprinklr upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Sprinklr måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Sprinklr Enkel inloggning](#configure-sprinklr-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Sprinklr-testanvändare](#create-sprinklr-test-user)** – om du vill ha en motsvarighet till Britta Simon i Sprinklr som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Sprinklr:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Sprinklr-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Sprinklr-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.sprinklr.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Sprinklr Client supportteam](https://www.sprinklr.com/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Sprinklr.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurera Sprinklr enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din Sprinklr-företagswebbplats som administratör.

1. Gå till **Administrationsinställningar \> **.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **Hantera partner \> enkel inloggning** från den vänstra rutan.

    ![Hantera partner](./media/sprinklr-tutorial/ic782908.png "Hantera partner")

1. Klicka på **+Lägg till enstaka inloggningar**.

    ![Enkel inloggning](./media/sprinklr-tutorial/ic782909.png "Enkel inloggning")

1. Gör följande på sidan **Enkel inloggning:**

    ![Enkel inloggning](./media/sprinklr-tutorial/ic782910.png "Enkel inloggning")

    a. Skriv ett namn för konfigurationen i textrutan **Namn** (till exempel *WAADSSOTest*).

    b. Välj **Aktiverad**.

    c. Välj **Använd nytt SSO-certifikat**.

    d. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **identitetsprovidercertifikat.**

    e. Klistra in **Azure AD-identifierare** som du har kopierat från Azure Portal i **entitets-ID-textrutan.**

    f. Klistra in värdet **för inloggnings-URL** som du har kopierat från Azure Portal i textrutan **för identitetsproviderns inloggningsadress.**

    g. Klistra in **url-värdet för utloggning** som du har kopierat från Azure Portal i **URL-textrutan för identitetsproviderinloggning.**

    h. Som **SAML-användar-ID-typ**väljer du **Kontroll innehåller användarens användarnamn sprinklr.com**.

    i. Som **SAML-användar-ID-plats**väljer du **Användar-ID i elementet Namnidentifierare i ämnesutdraget**.

    j. Klicka på **Spara**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Sprinklr.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Sprinklr**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Sprinklr**i programlistan .

    ![Länken Sprinklr i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sprinklr-test-user"></a>Skapa Sprinklr-testanvändare

1. Logga in på din Sprinklr-företagswebbplats som administratör.

1. Gå till **Administrationsinställningar \> **.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **Hantera klientanvändare \> ** från den vänstra rutan.

    ![Inställningar](./media/sprinklr-tutorial/ic782914.png "Inställningar")

1. Klicka på **Lägg till användare**.

    ![Inställningar](./media/sprinklr-tutorial/ic782915.png "Inställningar")

1. Gör följande i dialogrutan **Redigera användare:**

    ![Redigera användare](./media/sprinklr-tutorial/ic782916.png "Redigera användare")

    a. I textrutorna **E-post,** **förnamn** och **efternamn** skriver du informationen för ett Azure AD-användarkonto som du vill etablera.

    b. Välj **Lösenord inaktiverat**.

    c. Välj **Språk**.

    d. Välj **användartyp**.

    e. Klicka på **Uppdatera**.

    > [!IMPORTANT]
    > **Lösenord inaktiverat** måste väljas för att en användare ska kunna logga in via en identitetsleverantör. 

1. Gå till **Roll**och utför sedan följande steg:

    ![Partnerroller](./media/sprinklr-tutorial/ic782917.png "Partnerroller")

    a. Välj **ALL_Permissions**i listan **Globalt** .  

    b. Klicka på **Uppdatera**.

> [!NOTE]
> Du kan använda andra Verktyg för att skapa Sprinklr-användarkonton eller API:er som tillhandahålls av Sprinklr för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Sprinklr på åtkomstpanelen ska du automatiskt loggas in på den Sprinklr som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
