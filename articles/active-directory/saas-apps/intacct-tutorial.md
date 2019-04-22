---
title: 'Självstudier: Azure Active Directory-integrering med Intacct | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 31bc5622c6c6c3dd00bc59b5d8f3aa349055d125
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279252"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Självstudier: Azure Active Directory-integrering med Intacct

I den här självstudien får du lära dig hur du integrerar Intacct med Azure Active Directory (AD Azure).
Integrera Intacct med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Intacct.
* Du kan aktivera användarna att vara automatiskt inloggad till Intacct (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Intacct, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Intacct enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Intacct **IDP** -initierad SSO

## <a name="adding-intacct-from-the-gallery"></a>Att lägga till Intacct från galleriet

För att konfigurera integrering av Intacct i Azure AD, som du behöver lägga till Intacct från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Intacct från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Intacct**väljer **Intacct** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Intacct i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Intacct baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Intacct upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Intacct, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Intacct Single Sign-On](#configure-intacct-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Intacct](#create-intacct-test-user)**  – du har en motsvarighet för Britta Simon i Intacct som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Intacct:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Intacct** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Intacct domän och URL: er med enkel inloggning för information](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Intacct klienten supportteamet](https://us.intacct.com/support) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Intacct** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-intacct-single-sign-on"></a>Konfigurera Intacct Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din Intacct företagets webbplats som administratör.

1. Klicka på den **företagets** fliken och klicka sedan på **företagets information**.

    ![Företag](./media/intacct-tutorial/ic790037.png "Företag")

1. Klicka på den **Security** fliken och klicka sedan på **redigera**.

    ![Säkerhet](./media/intacct-tutorial/ic790038.png "Säkerhet")

1. I den **enkel inloggning (SSO)** avsnittet, utför följande steg:

    ![Enkel inloggning](./media/intacct-tutorial/ic790039.png "enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. Som **identifiera providertyp**väljer **SAML 2.0**.

    c. I **utfärdar-URL** textrutan klistra in värdet för **Azure Ad-identifierare** som du har kopierat från Azure-portalen.

    d. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    e. Öppna din **Base64-** kodade certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat** box.

    f. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Intacct.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Intacct**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Intacct**.

    ![Länken Intacct i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-intacct-test-user"></a>Skapa Intacct testanvändare

Om du vill konfigurera Azure AD-användare så att de kan logga in på Intacct, måste de etableras i Intacct. För Intacct är etablering en manuell aktivitet.

**Utför följande steg för att etablera användarkonton:**

1. Logga in på din **Intacct** klient.

1. Klicka på den **företagets** fliken och klicka sedan på **användare**.

    ![Användare](./media/intacct-tutorial/ic790041.png "Användare")

1. Klicka på den **Lägg till** fliken.

    ![Lägg till](./media/intacct-tutorial/ic790042.png "Lägg till")

1. I den **användarinformation** avsnittet, utför följande steg:

    ![Användarinformation](./media/intacct-tutorial/ic790043.png "användarinformation")

    a. Ange den **användar-ID**, **efternamn**, **Förnamn**, **e-postadress**, **rubrik**, och **Phone** på ett Azure AD-konto som du vill etablera i den **användarinformation** avsnittet.

    b. Välj den **administratörsprivilegier** på ett Azure AD-konto som du vill etablera.

    c. Klicka på **Spara**. Azure AD-kontoinnehavare tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den blir aktiv.

> [!NOTE]
> Du kan använda andra verktyg för Intacct att skapa användaren-konto eller API: er som tillhandahålls av Intacct för att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Intacct i åtkomstpanelen, bör det vara loggas in automatiskt till Intacct som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

