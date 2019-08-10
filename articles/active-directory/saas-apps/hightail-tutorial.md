---
title: 'Självstudier: Azure Active Directory integrering med Hightail | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7f4dd89b2469d99fadd08178dbca9c17382414a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945005"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Självstudier: Azure Active Directory integrering med Hightail

I den här självstudien får du lära dig hur du integrerar Hightail med Azure Active Directory (Azure AD).
Genom att integrera Hightail med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Hightail.
* Du kan göra det möjligt för användarna att logga in automatiskt till Hightail (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Hightail behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Hightail-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Hightail stöder **SP-och IDP** -INITIERAd SSO
* Hightail stöder **just-in-Time** User-etablering

## <a name="adding-hightail-from-the-gallery"></a>Lägga till Hightail från galleriet

Om du vill konfigurera integreringen av Hightail i Azure AD måste du lägga till Hightail från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Hightail från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Hightail**, väljer **Hightail** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Hightail i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Hightail baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Hightail upprättas.

Om du vill konfigurera och testa enkel inloggning med Hightail i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Hightail-enkel inloggning](#configure-hightail-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Hightail test User](#create-hightail-test-user)** – om du vill ha en motsvarighet till Britta Simon i Hightail som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Hightail i Azure AD:

1. Välj **enkel inloggning**på sidan **Hightail** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning för Hightail-domän och URL: er](common/both-replyurl.png)

    I text rutan svars- **URL** anger du URL: en:`https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE]
    > Svars-URL-värdet är inte ett reellt värde. Du uppdaterar svars-URL-värdet med den faktiska svars-URL: en som beskrivs senare i självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Hightail-domän och URL: er](common/both-signonurl.png)

    I text rutan **inloggnings-URL** anger du URL: en:`https://www.hightail.com/loginSSO`

6. Ditt Hightail-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

7. Utöver ovan förväntar sig Hightail-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | -------- |-------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | UserIdentity | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

9. I avsnittet **Konfigurera Hightail** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

    > [!NOTE]
    > Innan du konfigurerar den enkla inloggningen i Hightail-appen, måste du vara vit lista över din e-postdomän med Hightail-teamet så att alla användare som använder den här domänen kan använda enkel inloggnings funktioner.

### <a name="configure-hightail-single-sign-on"></a>Konfigurera Hightail enkel inloggning

1. Öppna administrations portalen för **Hightail** i ett annat webbläsarfönster.

2. Klicka på **användar ikonen** i det övre högra hörnet på sidan. 

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure1.png)

3. Klicka på fliken **Visa administratörs konsol** .

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure2.png)

4. I menyn högst upp klickar du på fliken **SAML** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure3.png)

    a. I text rutan inloggnings- **URL** klistrar du in värdet för inloggnings- **URL: en** som kopierats från Azure Portal

    b. Öppna ditt bas-64-kodade certifikat i anteckningar som hämtats från Azure Portal, kopiera innehållet i det till Urklipp och klistra in det i text rutan för **SAML-certifikatet** .

    c. Klicka på **Kopiera** för att kopiera SAML-konsument-URL: en för din instans och klistra in den i text rutan svars- **URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    d. Klicka på **Spara konfigurationer**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon\@yourcompanydomain. extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Hightail.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Hightail**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Hightail**.

    ![Hightail-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hightail-test-user"></a>Skapa Hightail test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Hightail. Hightail stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Hightail skapas en ny efter autentiseringen.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Hightail support-teamet](mailto:support@hightail.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Hightail på åtkomst panelen, bör du loggas in automatiskt på den Hightail som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)