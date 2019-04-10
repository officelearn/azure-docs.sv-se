---
title: 'Självstudier: Azure Active Directory-integrering med Tableau Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2ae0aefe194ca8bca6ea62420314b4fbdb1e0187
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357913"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Självstudier: Azure Active Directory-integrering med Tableau Server

I den här självstudien får du lära dig hur du integrerar Tableau Server med Azure Active Directory (AD Azure).
Integrera Tableau Server med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Tableau Server.
* Du kan aktivera användarna att vara automatiskt inloggad till Tableau Server (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tableau Server behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Tableau Server enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Tableau Server stöder **SP** -initierad SSO

## <a name="adding-tableau-server-from-the-gallery"></a>Att lägga till Tableau Server från galleriet

För att konfigurera integrering av Tableau Server i Azure AD, som du behöver lägga till Tableau Server från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Tableau Server från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Tableau Server**väljer **Tableau Server** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Tableau Server i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Tableau Server baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Tableau Server ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Server, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Tableau Server enkel inloggning](#configure-tableau-server-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Tableau Server testanvändare](#create-tableau-server-test-user)**  – du har en motsvarighet för Britta Simon i Tableau Server som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Server:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Tableau Server** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Tableau Server-domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://azure.<domain name>.link`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://azure.<domain name>.link`

    c. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Föregående värden är inte riktiga värden. Uppdatera värden med faktiska URL och identifierare från sidan Tableau Server konfiguration som beskrivs senare i självstudien.

5. Tableau Server-programmet förväntar sig ett anpassat anspråk **användarnamn** som måste definieras enligt nedan. Detta används som användar-ID i stället för unik identifierare för användaranspråk. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. Klicka på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](common/edit-attribute.png)

6. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | Namn | Källattribut | Namnområde |
    | ---------------| --------------- | ----------- |
    | användarnamn | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Ange den **Namespace** värde.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. På den **konfigurera Tableau Server** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurera Tableau Server enkel inloggning

1. För att få SSO konfigurerats för ditt program, måste du logga in på din Tableau Server-klient som administratör.

2. På den **CONFIGURATION** fliken **användarens identitet och åtkomst**, och välj sedan den **autentisering** metoden fliken.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. På den **CONFIGURATION** utför följande steg:

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. För **autentiseringsmetod**, Välj SAML.

    b. Markera kryssrutan för **aktivera SAML-autentisering för servern**.

    c. Tableau Server Retur-URL – URL: en som Tableau Server-användare kommer att använda, till exempel <http://tableau_server>. Med hjälp av `http://localhost` rekommenderas inte. Med en URL med ett avslutande snedstreck (till exempel `http://tableau_server/`) stöds inte. Kopiera **Tableau Server Retur-URL** och klistra in den i **inloggning på URL: en** -textrutan i **SAML grundkonfiguration** avsnitt i Azure-portalen

    d. SAML entitets-ID – entitets-ID som unikt identifierar din Tableau Server-installation att IDP: N. Du kan ange din Tableau Server URL: en igen här, om du vill, men den behöver inte vara din Tableau Server-URL. Kopiera **SAML entitets-ID** och klistra in den i **identifierare** -textrutan i **SAML grundkonfiguration** avsnitt i Azure-portalen

    e. Klicka på den **hämta XML-metadatafil** och öppna den i redigeringsprogrammet text. Leta upp URL för Konsumenttjänst för försäkran med Http Post och Index 0 och kopiera URL: en. Klistra in den i **svars-URL** -textrutan i **SAML grundkonfiguration** avsnitt i Azure-portalen

    f. Leta upp din Federationsmetadata-fil som hämtats från Azure-portalen och ladda upp den i den **IDP: N för SAML-metadatafil**.

    g. Ange namn för de attribut som IDP: N använder för att lagra användarnamn, visningsnamn och e-postadresser.

    h. Klicka på **Spara**

    > [!NOTE]
    > Kunden behöver ladda upp alla certifikat i Tableau Server SAML SSO-konfigurationen och kommer få ignoreras i flödet för enkel inloggning. Om du behöver för att konfigurera SAML på Tableau Server finns i den här artikeln [konfigurera SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Server.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Tableau Server**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Tableau Server**.

    ![Länken Tableau Server i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-tableau-server-test-user"></a>Skapa Tableau Server testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Tableau Server. Du måste etablera alla användare i Tableau server.

Det aktuella användarnamnet för användaren bör matcha värdet som du har konfigurerat i det anpassade attributet Azure AD av **användarnamn**. Integrationen bör fungera konfigurera Azure AD enkel inloggning med korrekt mappning.

> [!NOTE]
> Om du vill skapa en användare manuellt kan behöva du kontakta Tableau Server-administratör i din organisation.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau Server på åtkomstpanelen, bör det vara loggas in automatiskt till Tableau Server som du konfigurerar enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

