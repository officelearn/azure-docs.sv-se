---
title: 'Självstudiekurs: Azure Active Directory-integrering med Zscaler Beta | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943293"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Självstudiekurs: Azure Active Directory-integrering med Zscaler Beta

I den här självstudien lär du dig att integrera Zscaler Beta med Azure Active Directory (AD Azure).
När du integrerar Zscaler Beta med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler Beta.
* Tillåt att användarna automatiskt loggas in på Zscaler Beta med sina Azure AD-konton. Den här åtkomstkontrollen kallas enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Zscaler Beta behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En Zscaler Beta-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler Beta stöder SP-initierad SSO.
* Zscaler Beta stöder just-in-time-användaretablering.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Lägg till Zscaler Beta från Azure Marketplace

Om du vill konfigurera integreringen av Zscaler Beta i Azure AD lägger du till Zscaler Beta från Azure Marketplace i listan över hanterade SaaS-appar.

Så här lägger du till Zscaler Beta från Azure Marketplace.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i det vänstra navigeringsfönstret .

    ![Knappen Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Ange **Zscaler Beta**i sökrutan . Välj **Zscaler Beta** på resultatpanelen och välj sedan **Lägg till**.

     ![Zscaler Beta i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Zscaler Beta baserat på testanvändaren Britta Simon.
För enkel inloggning för att arbeta upprättar du en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zscaler Beta.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Zscaler Beta slutför du följande byggblock:

- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- Konfigurera enkel inloggning i [Zscaler Beta](#configure-zscaler-beta-single-sign-on) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
- [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
- [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
- [Skapa en Zscaler Beta-testanvändare](#create-a-zscaler-beta-test-user) för att ha en motsvarighet till Britta Simon i Zscaler Beta som är länkad till Azure AD-representationen av användaren.
- [Testa enkel inloggning](#test-single-sign-on) för att verifiera huruvida konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Zscaler Beta.

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Zscaler Beta**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera en inloggningslänk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration.** **Set up Single Sign-On with SAML**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** följer du det här steget:

    ![Zscaler Beta domän och webbadresser enkel inloggningsinformation](common/sp-intiated.png)

    - I rutan **Logga in på URL** anger du den URL som användarna använder för att logga in på zscaler betaprogrammet.

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med det faktiska värdet För inloggnings-URL. Kontakta [Zscaler Beta-klientsupportteamet](https://www.zscaler.com/company/contact)för att få värdet .

5. Zscaler Beta-programmet förväntar sig SAML-påståenden i ett visst format. Du måste lägga till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Välj **Redigera** om du vill öppna dialogrutan **Användarattribut.**

    ![Dialogrutan Användarattribut](common/edit-attribute.png)

6. Zscaler Beta ansökan förväntar sig några fler attribut som skall skickas tillbaka i SAML svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** följer du dessa steg för att lägga till ATTRIBUTEt SAML-token, som visas i följande tabell.
    
    | Namn | Källattribut | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Dialogrutan Användaranspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    b. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    c. Lämna **rutan Namnområde** tom.

    d. För **Källa**väljer du **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Välj **OK**.

    g. Välj **Spara**.

    > [!NOTE]
    > Mer information om hur du konfigurerar roller i Azure AD finns i [Konfigurera rollanspråket](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Välj **Hämta** i avsnittet **SAML-signeringscertifikat** i avsnittet Konfigurera **enkel inloggning med SAML-signeringscertifikat** för att hämta **certifikatet (Base64).** Spara den på datorn.

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera Zscaler Beta** kopierar du de webbadresser du behöver för dina behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - Inloggnings-URL
    - Azure AD-identifierare
    - Utloggnings-URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurera enkel inloggning i Zscaler Beta

1. Om du vill automatisera konfigurationen i Zscaler Beta installerar du **webbläsartillägget My Apps Secure Sign-in** genom att välja **Installera tillägget**.

    ![Tillägget Mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren skickar du in **Konfigurera Zscaler Beta** till Zscaler Beta-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Zscaler Beta. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3 till och med 6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Zscaler Beta manuellt öppnar du ett nytt webbläsarfönster. Logga in på zscaler beta-företagets webbplats som administratör och följ dessa steg.

4. Gå till**Authentication** > **Autentiseringsinställningar för** **administrationsautentisering** > och följ dessa steg.
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administration")

    a. Under **Autentiseringstyp**väljer du **SAML**.

    b. Välj **Konfigurera SAML**.

5. Gör så här i fönstret **Redigera SAML:** 
            
    ![Hantera användare & autentisering](./media/zscaler-beta-tutorial/ic800208.png "Hantera användare & autentisering")
    
    a. Klistra in **inloggnings-URL:en** som du kopierade från Azure-portalen i **url-adressen för SAML Portal.**

    b. I rutan **Attribut för inloggningsnamn** anger du **NameID**.

    c. I rutan **Offentligt SSL-certifikat** väljer du **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du hämtade från Azure-portalen.

    d. Växla **Aktivera SAML-automatisk etablering**.

    e. I rutan **Attribut för användarnamn** anger du **displayName** om du vill aktivera SAML-automatisk etablering för displayName-attribut.

    f. I rutan **Attribut för gruppnamn** anger du **memberOf** om du vill aktivera SAML-automatisk etablering för memberOf-attribut.

    g. Ange avdelning **i** rutan Attribut **för avdelning** om du vill aktivera SAML-automatisk etablering för avdelningsattribut.

    h. Välj **Spara**.

6. Gör så här på dialogrutan **Konfigurera användarautentisering:**

    ![Aktiveringsmenyn och knappen Aktivera](./media/zscaler-beta-tutorial/ic800207.png)

    a. Hovra över **aktiveringsmenyn** längst ned till vänster.

    b. Välj **Aktivera**.

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar
SÃ¥ hÃ¤r konfigurerar du proxyinställningarna i Internet Explorer.

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** på **Verktyg-menyn** för att öppna dialogrutan **Internetalternativ.** 
    
     ![Dialogrutan Internet-alternativ](./media/zscaler-beta-tutorial/ic769492.png "Internet-alternativ")

3. Välj fliken **Anslutningar.** 
  
     ![Fliken Anslutningar](./media/zscaler-beta-tutorial/ic769493.png "Anslutningar")

4. Välj **LAN-inställningar** för att öppna dialogrutan **Inställningar för lokalt nätverk (LAN).**

5. Gör så här i avsnittet **Proxyserver:** 
   
    ![Avsnittet Proxyserver](./media/zscaler-beta-tutorial/ic769494.png "Proxyserver")

    a. Markera kryssrutan **Använd en proxyserver för ditt LAN.**

    b. Ange **Address** gateway i rutan **Adress. Zscaler Beta.net**.

    c. Ange **80**i rutan **Port** .

    d. Markera kryssrutan **Kringgå proxyserver för lokala adresser.**

    e. Välj **OK** för att stänga dialogrutan **Inställningar för lokalt nätverk (LAN).**

6. Välj **OK** för att stänga dialogrutan **Internetalternativ.**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. I Den vänstra rutan i Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

    ![Länkar för användare och alla användare](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Gör så här i dialogrutan **Användare:**

    ![Dialogrutan Användare](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I rutan **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`. Ett exempel är BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord.** Skriv ned värdet som visas i rutan **Lösenord.**

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

Aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till Zscaler Beta.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** > **Zscaler Beta**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan anger du och väljer **Zscaler Beta**.

    ![Zscaler Beta länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länkar till användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Knappen Lägg till användare](common/add-assign-user.png)

5. Välj användaren som **Britta Simon** i listan i dialogrutan **Användare och grupper.** Välj sedan **Välj** längst ned på skärmen.

    ![Dialogrutan Användare och grupper](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Välj lämplig användarroll i listan i dialogrutan **Välj roll.** Välj sedan **Välj** längst ned på skärmen.

    ![Dialogrutan Välj roll](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

    ![Dialogrutan Lägg till tilldelning](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Skapa en Zscaler Beta testanvändare

I det här avsnittet skapas användaren Britta Simon i Zscaler Beta. Zscaler Beta stöder **just-in-time-användaretablering**, vilket är aktiverat som standard. Det finns inget du kan göra i det här avsnittet. Om det inte redan finns någon användare i Zscaler Beta skapas en ny efter autentisering.

>[!Note]
>Om du vill skapa en användare manuellt kontaktar du [Zscaler Beta-supportteamet](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Testa din azure AD-konfiguration med hjälp av åtkomstpanelen.

När du väljer Zscaler Beta-panelen på åtkomstpanelen ska du automatiskt loggas in på den Zscaler Beta som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

