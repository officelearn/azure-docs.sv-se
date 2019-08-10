---
title: 'Självstudier: Azure Active Directory integrering med TextMagic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 53711a80f35177c911b754a0ce3f189ac106adb2
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943308"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Självstudier: Azure Active Directory integrering med TextMagic

I den här självstudien får du lära dig hur du integrerar TextMagic med Azure Active Directory (Azure AD).
Genom att integrera TextMagic med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TextMagic.
* Du kan göra det möjligt för användarna att logga in automatiskt till TextMagic (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TextMagic behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TextMagic-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TextMagic stöder **IDP** INITIERAd SSO
* TextMagic stöder **just-in-Time** User-etablering

## <a name="adding-textmagic-from-the-gallery"></a>Lägga till TextMagic från galleriet

Om du vill konfigurera integreringen av TextMagic i Azure AD måste du lägga till TextMagic från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till TextMagic från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **TextMagic**, väljer **TextMagic** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![TextMagic i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TextMagic baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i TextMagic upprättas.

Om du vill konfigurera och testa enkel inloggning med TextMagic i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera TextMagic-enkel inloggning](#configure-textmagic-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa TextMagic test User](#create-textmagic-test-user)** – om du vill ha en motsvarighet till Britta Simon i TextMagic som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med TextMagic i Azure AD:

1. Välj **enkel inloggning**på sidan **TextMagic** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för TextMagic-domän och URL: er](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://my.textmagic.com/saml/metadata`

5. Ditt TextMagic-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. TextMagic-programmet förväntar sig att **NameIdentifier** mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

6. Utöver ovan förväntar sig TextMagic-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn |   Källattribut| Namnrymd  |
    | --------------- | --------------- | --------------- |
    | Företagets | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefon               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I textrutan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera TextMagic** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-textmagic-single-sign-on"></a>Konfigurera TextMagic enkel inloggning

1. Logga in på din TextMagic-företags webbplats som administratör i ett annat webbläsarfönster.

2. Välj **konto inställningar** under användar namnet.

    ![TextMagic-konfiguration](./media/textmagic-tutorial/config1.png)

3. Klicka på fliken **enkel inloggning (SSO)** och fyll i följande fält:  

    ![TextMagic-konfiguration](./media/textmagic-tutorial/config2.png)

    a. I text rutan **entitets-ID för identitetsprovider:** klistrar du in värdet för **Azure AD**-identifieraren, som du har kopierat från Azure Portal.

    b. Klistra in värdet för inloggnings- **URL**: en som du har kopierat från Azure Portal i **URL: en för URL för identitetsprovider** .

    c. I **URL: en för URL för identitets leverantör** , klistra in värdet för **Logga ut URL**, som du har kopierat från Azure Portal.

    d. Öppna ditt **bas-64-kodade certifikat** i anteckningar som hämtats från Azure Portal, kopiera innehållet i det till Urklipp och klistra in det i text rutan för det **offentliga x509-certifikatet:** .

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I **brittasimon@yourcompanydomain.extension** fältet **användar namn** . Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TextMagic.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **TextMagic**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TextMagic**.

    ![TextMagic-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-textmagic-test-user"></a>Skapa TextMagic test användare

Programmet stöder **just-in-Time-etablering** och efter att användare av autentisering skapas i programmet automatiskt. Du måste fylla i informationen en gång vid den första inloggningen för att aktivera under kontot i systemet.
Det finns inget åtgärdsobjekt för dig i det här avsnittet.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TextMagic på åtkomst panelen, bör du loggas in automatiskt på den TextMagic som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)