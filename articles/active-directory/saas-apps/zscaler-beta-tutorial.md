---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler Beta | Microsoft Docs'
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
ms.openlocfilehash: f31361dc3d7e24092677f1a78b2c405ae84578ed
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230054"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Självstudier: Azure Active Directory-integrering med Zscaler Beta

I den här självstudien lär du dig att integrera Zscaler Beta med Azure Active Directory (AD Azure).
När du integrerar Zscaler Beta med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Zscaler Beta.
* Tillåt användarna att logga in automatiskt till Zscaler Beta med sina Azure AD-konton. Den här åtkomstkontroll kallas för enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure portal.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Zscaler Beta behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En Zscaler Beta-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler Beta stöder SP-initierad SSO.
* Zscaler Beta stöder just-in-time-användaretablering.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Lägg till Zscaler Beta från Azure Marketplace

Lägg till Zscaler Beta från Azure Marketplace till din lista över hanterade SaaS-appar för att konfigurera integrering av Zscaler Beta i Azure AD.

Följ dessa steg för att lägga till Zscaler Beta från Azure Marketplace.

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret väljer **Azure Active Directory**.

    ![Knappen Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Zscaler Beta**. Välj **Zscaler Beta** resultatet panelen och välj sedan **Lägg till**.

     ![Zscaler Beta i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler Beta utifrån testanvändare Britta Simon.
För enkel inloggning att fungera, skapar du en länk-relation mellan en Azure AD-användare och relaterade användaren Zscaler Beta.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler Beta, utför du följande byggblock:

- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- [Konfigurera Zscaler Beta enkel inloggning](#configure-zscaler-beta-single-sign-on) att konfigurera inställningar för enkel inloggning på programsidan.
- [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
- [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
- [Skapa en testanvändare Zscaler Beta](#create-a-zscaler-beta-test-user) har en motsvarighet för Britta Simon i betaversionen av Zscaler som är länkad till en Azure AD-representation av användaren.
- [Testa enkel inloggning](#test-single-sign-on) för att verifiera huruvida konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Följ dessa steg om du vill konfigurera Azure AD enkel inloggning med Zscaler Beta.

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Zscaler Beta**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer **redigera** att öppna den **SAML grundkonfiguration** dialogrutan.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I den **SAML grundkonfiguration** , följer du det här steget:

    ![Zscaler Beta-domän och URL: er med enkel inloggning för information](common/sp-intiated.png)

    - I den **inloggnings-URL** anger den URL som används av användarna för att logga in på ditt Zscaler Beta-program.

    > [!NOTE]
    > Värdet inte existerar. Uppdatera värdet med faktiska inloggnings-URL-värdet. För att få värdet kan kontakta den [Zscaler Beta klienten supportteamet](https://www.zscaler.com/company/contact).

5. Zscaler Beta-program som förväntar SAML-intyg i ett visst format. Du måste lägga till anpassade attributmappningar SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Välj **redigera** att öppna den **användarattribut** dialogrutan.

    ![Användardialogrutan för attribut](common/edit-attribute.png)

6. Zscaler Beta-program som förväntar få några fler attribut som ska skickas tillbaka i SAML-svar. I den **användaranspråk** i avsnittet den **användarattribut** dialogrutan, följer du stegen för att lägga till attributet SAML-token som visas i följande tabell.
    
    | Namn | Källattribut | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Användardialogrutan för anspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    b. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    c. Lämna den **Namespace** lämnas omarkerad.

    d. För **källa**väljer **attributet**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Välj **OK**.

    g. Välj **Spara**.

    > [!NOTE]
    > Information om hur du konfigurerar roller i Azure AD finns [konfigurera rollen anspråket](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer **hämta** att ladda ned den **certifikat (Base64)** . Spara den på din dator.

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

8. I den **konfigurera Zscaler Beta** avsnittet, Kopiera URL: er som du behöver för dina behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - Inloggningswebbadress
    - Microsoft Azure Active Directory-identifierare
    - Utloggnings-URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurera Zscaler Beta enkel inloggning

1. Om du vill automatisera konfigurationen inom Zscaler Beta, installera **Mina appar skyddat inloggning webbläsartillägget** genom att välja **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägget i webbläsaren väljer **konfigurera Zscaler Beta** dirigerar dig till Zscaler Beta-programmet. Ange administratörsautentiseringsuppgifter för att logga in på Zscaler Beta därifrån. Webbläsartillägget konfigurerar programmet åt dig automatiskt och automatiserar steg 3 till 6.

    ![Installationskonfiguration](common/setup-sso.png)

3. Ställ in Zscaler Beta manuellt genom att öppna ett nytt webbläsarfönster. Logga in på webbplatsen Zscaler Beta företagets som administratör och följ stegen nedan.

4. Gå till **Administration** > **autentisering** > **autentiseringsinställningar**, och följ stegen nedan.
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administration")

    a. Under **autentiseringstyp**väljer **SAML**.

    b. Välj **konfigurerar du SAML**.

5. I den **redigera SAML** fönstret gör så här: 
            
    ![Hantera användare och autentisering](./media/zscaler-beta-tutorial/ic800208.png "Hantera användare och autentisering")
    
    a. I den **URL för SAML-Portal** rutan, klistra in den **inloggnings-URL** som du kopierade från Azure-portalen.

    b. I den **inloggning namnattributet** anger **NameID**.

    c. I den **offentliga SSL-certifikat** väljer **överför** att ladda upp Azure SAML-signeringscertifikat som du laddade ned från Azure-portalen.

    d. Visa/dölj **aktivera SAML Automatisk etablering**.

    e. I den **användarnamnsattributet för visa** anger **displayName** om du vill aktivera SAML autoprovisioning för displayName-attribut.

    f. I den **Gruppnamnattributet** anger **memberOf** om du vill aktivera SAML autoprovisioning för memberOf attribut.

    g. I den **avdelning namnattributet** anger **avdelning** om du vill aktivera SAML autoprovisioning för avdelningsattribut.

    h. Välj **Spara**.

6. På den **konfigurera användarautentisering** dialogrutan utför de här stegen:

    ![Menyn för aktivering och knappen Aktivera](./media/zscaler-beta-tutorial/ic800207.png)

    a. Hovra över den **aktivering** menyn längst ned till vänster.

    b. Välj **aktivera**.

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar
Följ dessa steg om du vill konfigurera proxyinställningarna i Internet Explorer.

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** från den **verktyg** menyn för att öppna den **Internetalternativ** dialogrutan. 
    
     ![Dialogrutan Internetalternativ](./media/zscaler-beta-tutorial/ic769492.png "Internet-alternativ")

3. Välj den **anslutningar** fliken. 
  
     ![Fliken anslutningar](./media/zscaler-beta-tutorial/ic769493.png "anslutningar")

4. Välj **LAN-inställningar** att öppna den **inställningar för lokalt nätverk (LAN)** dialogrutan.

5. I den **proxyserver** , följer du dessa steg: 
   
    ![Proxy serveravsnittet](./media/zscaler-beta-tutorial/ic769494.png "proxyserver")

    a. Välj den **använder en proxyserver för ditt lokala nätverk** markerar du kryssrutan.

    b. I den **adress** anger **gateway. Zscaler Beta.net**.

    c. I den **Port** anger **80**.

    d. Välj den **Använd ingen proxyserver för lokala adresser** markerar du kryssrutan.

    e. Välj **OK** att Stäng den **inställningar för lokalt nätverk (LAN)** dialogrutan.

6. Välj **OK** att Stäng den **Internetalternativ** dialogrutan.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen går du till den vänstra panelen och väljer **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användare och alla användare länkar](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I den **användaren** dialogrutan följer du dessa steg:

    ![Användardialogrutan](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** anger `brittasimon@yourcompanydomain.extension`. Ett exempel är BrittaSimon@contoso.com.

    c. Välj den **Show lösenord** markerar du kryssrutan. Skriv ned värdet som visas i den **lösenord** box.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

Aktivera Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Zscaler Beta.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **Zscaler Beta**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Ange i listan med program och välj **Zscaler Beta**.

    ![Zscaler Beta länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länk för användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare**. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Knappen Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan, Välj användaren som **Britta Simon** i listan. Välj sedan **Välj** längst ned på skärmen.

    ![Dialogrutan användare och grupper](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. I den **Välj roll** dialogrutan väljer du en lämplig användarroll i listan. Välj sedan **Välj** längst ned på skärmen.

    ![Dialogrutan Välj roll](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

    ![Tilldelning av dialogrutan Lägg till](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Skapa en testanvändare Zscaler Beta

I det här avsnittet skapas användaren Britta Simon Zscaler Beta. Zscaler Beta stöder **just-in-time-användaretablering**, vilket är aktiverat som standard. Det finns inget att göra i det här avsnittet. Om det inte redan finns någon användare i Zscaler Beta skapas en ny efter autentisering.

>[!Note]
>Skapa en användare manuellt genom att kontakta den [Zscaler Beta-supportteamet](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Zscaler Beta i åtkomstpanelen, bör det vara loggas in automatiskt till betaversionen av Zscaler som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

