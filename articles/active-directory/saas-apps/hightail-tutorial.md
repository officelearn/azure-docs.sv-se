---
title: 'Självstudier: Azure Active Directory-integrering med Hightail | Microsoft Docs'
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
ms.openlocfilehash: b57786aaf699f562743bfdd6cf5c2d8f1b41b0d7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276090"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Självstudier: Azure Active Directory-integrering med Hightail

I den här självstudien får du lära dig hur du integrerar Hightail med Azure Active Directory (AD Azure).
Integrera Hightail med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Hightail.
* Du kan aktivera användarna att vara automatiskt inloggad till Hightail (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Hightail, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Hightail enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Hightail stöder **SP och IDP** -initierad SSO
* Hightail stöder **Just In Time** etableringen av användare

## <a name="adding-hightail-from-the-gallery"></a>Att lägga till Hightail från galleriet

För att konfigurera integrering av Hightail i Azure AD, som du behöver lägga till Hightail från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Hightail från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Hightail**väljer **Hightail** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Hightail i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Hightail baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Hightail upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Hightail, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Hightail enkel inloggning](#configure-hightail-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Hightail](#create-hightail-test-user)**  – du har en motsvarighet för Britta Simon i Hightail som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Hightail:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Hightail** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Hightail domän och URL: er enkel inloggning för information](common/both-replyurl.png)

    I den **svars-URL** text skriver du URL: en som:  `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE]
    > Svars-URL-värdet är inte verkliga värdet. Du uppdaterar svars-URL-värdet med faktiska svars-URL som beskrivs senare i självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Hightail domän och URL: er enkel inloggning för information](common/both-signonurl.png)

    I den **inloggnings-URL** text skriver du URL: en som:  `https://www.hightail.com/loginSSO`

6. Programmets Hightail förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

7. Dessutom för att senare Hightail program förväntar sig att få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | -------- |-------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.mail |
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

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. På den **konfigurera Hightail** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

    > [!NOTE]
    > Innan du konfigurerar den enkel inloggning på Hightail app, kan du lista för tillåten din e-postdomän med Hightail team så att alla användare som använder den här domänen kan använda enkel inloggning funktioner.

### <a name="configure-hightail-single-sign-on"></a>Konfigurera Hightail enkel inloggning

1. Öppna i ett nytt webbläsarfönster i **Hightail** administrationsportalen.

2. Klicka på **Användarikon** från det övre högra hörnet på sidan. 

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure1.png)

3. Klicka på **visa administratörskonsolen** fliken.

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure2.png)

4. Klicka på menyn längst upp i **SAML** fliken och utför följande steg:

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure3.png)

    a. I den **inloggnings-URL** textrutan klistra in värdet för **inloggnings-URL** kopieras från Azure-portalen.

    b. Öppna din Base64-kodat certifikat i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **SAML-certifikatet** textrutan.

    c. Klicka på **kopia** Kopiera URL för SAML-konsument för din instans och klistra in den i **svars-URL** -textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

    d. Klicka på **spara konfigurationer**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Hightail.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Hightail**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Hightail**.

    ![Länken Hightail i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hightail-test-user"></a>Skapa Hightail testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Hightail. Hightail stöder just-in-time användaretablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Hightail, skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta den [Hightail supportteamet](mailto:support@hightail.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Hightail i åtkomstpanelen, bör det vara loggas in automatiskt till Hightail som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)