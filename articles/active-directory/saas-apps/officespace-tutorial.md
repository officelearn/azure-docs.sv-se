---
title: 'Självstudier: Azure Active Directory-integrering med OfficeSpace programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OfficeSpace programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 42ef2d9bbdf825eb0a872e9fe6fa66b34faa2cad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180576"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Självstudier: Azure Active Directory-integrering med OfficeSpace programvara

I den här självstudien får du lära dig hur du integrerar OfficeSpace programvara med Azure Active Directory (AD Azure).
Integrera OfficeSpace programvara med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till OfficeSpace programvara.
* Du kan aktivera användarna att vara automatiskt inloggad OfficeSpace programvara (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OfficeSpace programvara, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* OfficeSpace programvara enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Stöder OfficeSpace program **SP** -initierad SSO

* Stöder OfficeSpace program **Just In Time** etableringen av användare

## <a name="adding-officespace-software-from-the-gallery"></a>Lägga till OfficeSpace programvara från galleriet

För att konfigurera integrering av OfficeSpace programvara i Azure AD, som du behöver lägga till OfficeSpace programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OfficeSpace programvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **OfficeSpace programvara**väljer **OfficeSpace programvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![OfficeSpace programvara i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OfficeSpace programvara baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i OfficeSpace programvara ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med OfficeSpace programvara, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för OfficeSpace-programvara](#configure-officespace-software-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa OfficeSpace programvara testanvändare](#create-officespace-software-test-user)**  – du har en motsvarighet för Britta Simon OfficeSpace programvara som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med OfficeSpace programvara:

1. I den [Azure-portalen](https://portal.azure.com/)på den **OfficeSpace programvara** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![OfficeSpace programvara domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [OfficeSpace klientprogrammet supportteamet](mailto:support@officespacesoftware.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. OfficeSpace programvara förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. OfficeSpace programvara förväntar sig **nameidentifier** mappas med **user.mail**, så måste du redigera attribut mappar genom att klicka på **redigera** ikon och ändra attributmappning.

    ![image](common/edit-attribute.png)

6. Dessutom ovan OfficeSpace programvara program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ---------------| --------------- |
    | e-post | user.mail |
    | namn | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

8. I avsnittet **SAML-signeringscertifikat** kopierar du **Tumavtryck** och sparar det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

9. På den **konfigurera OfficeSpace programvara** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-officespace-software-single-sign-on"></a>Konfigurera OfficeSpace programvara enkel inloggning

1. Logga in på din klient för OfficeSpace programvara som en administratör i ett annat webbläsarfönster.

2. Gå till **inställningar** och klicka på **Anslutningsappar**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Klicka på **SAML-autentisering**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Gör följande i avsnittet **SAML-autentisering**:

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. I den **utloggnings-url för providern** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.

    b. I den **klienten idp målwebbadressen** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. Klistra in den **tumavtryck** värde som du har kopierat från Azure-portalen i den **klienten IDP certifikat fingeravtryck** textrutan. 

    d. Klicka på **Spara inställningar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OfficeSpace programvara.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **OfficeSpace programvara**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **OfficeSpace programvara**.

    ![Länken OfficeSpace programvara i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-officespace-software-test-user"></a>Skapa OfficeSpace programvara testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon OfficeSpace programvara. OfficeSpace programvara stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i OfficeSpace programvara, skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta [OfficeSpace programvara supportteamet](mailto:support@officespacesoftware.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen OfficeSpace programvara i åtkomstpanelen, bör det vara loggas in automatiskt till OfficeSpace programvara som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

