---
title: 'Självstudier: Azure Active Directory integrering med SD-element | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SD-element.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: a0272de238055151e73c13687f25745c3f402eb4
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826154"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Självstudier: Azure Active Directory integrering med SD-element

I den här självstudien får du lära dig att integrera SD-element med Azure Active Directory (Azure AD).
Genom att integrera SD-element med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till SD-element.
* Du kan göra det möjligt för användarna att logga in automatiskt till SD-element (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SD-element behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Den aktiverade prenumerationen för enkel inloggning för SD-element

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SD-element stöder **IDP** -INITIERAd SSO

## <a name="adding-sd-elements-from-the-gallery"></a>Lägga till SD-element från galleriet

Om du vill konfigurera integrering av SD-element i Azure AD måste du lägga till SD-element från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SD-element från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SD-element**, väljer **SD-element** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![SD-element i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med SD-element baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i SD-element upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med SD-element måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för SD-element](#configure-sd-elements-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa SD-element testa användare](#create-sd-elements-test-user)** – för att få en motsvarighet till Britta Simon i SD-element som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SD-element i Azure AD:

1. På sidan för **SD** -elementens program integration i [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning för SD-element och-URL](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [support teamet för SD-element](mailto:support@sdelements.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Dina program med SD-element förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn |  Källattribut|
    | --- | --- |
    | email |user.mail |
    | förnamn |user.givenname |
    | lastname |user.surname |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera SD-element** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-sd-elements-single-sign-on"></a>Konfigurera enkel inloggning för SD-element

1. Om du vill få enkel inloggning aktive rad kontaktar du [support teamet för SD-element](mailto:support@sdelements.com) och ger dem den hämtade certifikat filen.

1. Logga in på dina SD-element i ett annat webbläsarfönster som administratör.

1. I menyn högst upp klickar du på **system**och sedan **på enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. I dialog rutan **Inställningar för enkel inloggning** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Som **SSO-typ**väljer du **SAML**.

    b. I text rutan för **entitets-ID för identitetsprovider** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. Klistra in värdet för inloggnings- **URL: en**som du har kopierat från Azure Portal i text rutan för **enkel inloggning i identitetsprovider** .

    d. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SD-element.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SD-element**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SD-element**.

    ![SD-element länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sd-elements-test-user"></a>Skapa SD-element testa användare

Syftet med det här avsnittet är att skapa en användare med namnet Britta Simon i SD-element. När det gäller SD-element skapar SD-element användare en manuell uppgift.

**Utför följande steg för att skapa Britta Simon i SD-element:**

1. Logga in på dina SD-element på företags platsen som administratör i ett webbläsarfönster.

1. Klicka på **användar hantering**på menyn längst upp och sedan på **användare**.

    ![Skapa ett SD-element test användare](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klicka på **Lägg till ny användare**.

    ![Skapa ett SD-element test användare](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. I dialog rutan **Lägg till ny användare** utför du följande steg:

    ![Skapa ett SD-element test användare](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. I text rutan **e-post** anger du e-postmeddelandet för användaren **brittasimon@contoso.com** .

    b. I textrutan **Förnamn** skriver du förnamnet på användaren som **Britta**.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    d. Som **roll**väljer du **användare**.

    e. Klicka på **Skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen för SD-element på åtkomst panelen, bör du loggas in automatiskt på de SD-element som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)