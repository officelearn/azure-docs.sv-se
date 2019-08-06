---
title: 'Självstudier: Azure Active Directory integrering med Tableau-Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Tableau-servern.
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
ms.openlocfilehash: f9ef179c1a93d8b2f97c47eb4c68d0312d55d3d1
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825976"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Självstudier: Azure Active Directory integrering med Tableau-Server

I den här självstudien får du lära dig att integrera Tableau-servern med Azure Active Directory (Azure AD).
Genom att integrera Tableau-servern med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Tableau-servern.
* Du kan låta dina användare vara automatiskt inloggade på Tableau Server (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tableau-servern behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Tableau Server, enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Tableau Server stöder **SP** -INITIERAd SSO

## <a name="adding-tableau-server-from-the-gallery"></a>Lägga till Tableau-Server från galleriet

Om du vill konfigurera integrering av Tableau-servern i Azure AD måste du lägga till Tableau-servern från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Tableau-servern från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Tableau Server**, väljer **Tableau Server** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Tableau-server i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Tableau-servern baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Tableau-servern upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Tableau Server måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Tableau Server Single Sign-on](#configure-tableau-server-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Tableau Server test User](#create-tableau-server-test-user)** – om du vill ha en motsvarighet till Britta Simon på Tableau-server som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med Tableau-servern:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Tableau Server** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Tableau-Server-domän och URL-adresser](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://azure.<domain name>.link`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://azure.<domain name>.link`

    c. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Föregående värden är inte verkliga värden. Uppdatera värdena med den faktiska URL: en och identifieraren från sidan för konfiguration av Tableau-servern som beskrivs senare i självstudien.

5. Tableau Server-programmet förväntar sig ett anpassat anspråk **användar namn** som måste definieras enligt nedan. Detta används som användar identifierare i stället för ett unikt anspråk för användar-ID. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. Klicka på knappen **Redigera** för att öppna dialog rutan **användarattribut & anspråk** .

    ![image](common/edit-attribute.png)

6. I avsnittet **användar anspråk** i dialog rutan användarattribut **& anspråk** konfigurerar du SAML-token som visas i bilden ovan och utför följande steg:

    | Namn | Källattribut | Namnrymd |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Ange **namn områdets** värde.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

8. I avsnittet **Konfigurera Tableau-Server** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurera enkel inloggning för Tableau Server

1. För att få SSO konfigurerat för ditt program måste du logga in på Tableau-serverns klient organisation som administratör.

2. På fliken **konfiguration** väljer du **användar identitet & åtkomst**och väljer sedan fliken **autentiseringsmetod** .

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Utför följande steg på sidan **konfiguration** :

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Som **autentiseringsmetod**väljer du SAML.

    b. Markera kryss rutan för **Aktivera SAML-autentisering för servern**.

    c. Tableau Server retur-URL – den URL som Tableau-användare kommer att komma åt, till <http://tableau_server>exempel. Använd `http://localhost` rekommenderas inte. Användning av en URL med ett avslutande snedstreck (till exempel `http://tableau_server/`) stöds inte. Kopiera **Tableau Server Return URL** och klistra in den i text rutan för att **signera URL-adresser** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal

    d. SAML-entitets-ID – entitets-ID: t identifierar unikt Tableau-Server installationen till IdP. Du kan ange din Tableau-server-URL igen, om du vill, men den behöver inte vara Tableau-serverns URL. Kopiera **SAML entitets-ID** och klistra in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal

    e. Klicka på **filen Ladda ned XML-metadata** och öppna den i text redigerings programmet. Hitta intygets konsument tjänst-URL med http post och index 0 och kopiera URL: en. Klistra in den i text rutan för svars- **URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal

    f. Leta upp din federationsmetadata som hämtades från Azure Portal och ladda sedan upp den i **SAML-IDP metadata-fil**.

    g. Ange namnen på de attribut som IdP använder för att lagra användar namn, visnings namn och e-postadresser.

    h. Klicka på **Spara**

    > [!NOTE]
    > Kunden måste ladda upp certifikat i Tableau-serverns SAML SSO-konfiguration och den kommer att ignoreras i SSO-flödet. Om du behöver hjälp med att konfigurera SAML på Tableau-servern kan du läsa den här artikeln [Konfigurera SAML](https://help.tableau.com/current/server/en-gb/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** anger du`brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Tableau-servern.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Tableau Server**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Tableau-Server**.

    ![Länken Tableau server i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-tableau-server-test-user"></a>Skapa Tableau-Server test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Tableau Server. Du måste etablera alla användare på Tableau-servern.

Användar namnet för användaren ska matcha det värde som du har konfigurerat i det anpassade Azure AD-attributet för **användar namn**. Med rätt mappning bör integrationen fungera för att konfigurera enkel inloggning för Azure AD.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta Tableau-serverns administratör i din organisation.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau server på åtkomst panelen, bör du loggas in automatiskt på den Tableau-server som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

