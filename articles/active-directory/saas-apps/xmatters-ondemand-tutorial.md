---
title: 'Självstudier: Azure Active Directory-integrering med xMatters OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 4217f85394d66c3da116cf92767ad1da8ad7e799
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920196"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Självstudier: Azure Active Directory-integrering med xMatters OnDemand

I den här självstudien får du lära dig hur du integrerar xMatters OnDemand med Azure Active Directory (AD Azure).
Integrera xMatters OnDemand med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till xMatters OnDemand.
* Du kan aktivera användarna att vara automatiskt inloggad till xMatters OnDemand (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med xMatters OnDemand, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* xMatters OnDemand enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* xMatters OnDemand stöder **IDP** -initierad SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Att lägga till xMatters OnDemand från galleriet

För att konfigurera integrering av xMatters OnDemand i Azure AD, som du behöver lägga till xMatters OnDemand från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till xMatters OnDemand från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **xMatters OnDemand**väljer **xMatters OnDemand** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![xMatters OnDemand i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med xMatters OnDemand baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning att fungera, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i xMatters OnDemand måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med xMatters OnDemand, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera xMatters OnDemand Single Sign-On](#configure-xmatters-ondemand-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa xMatters OnDemand testanvändare](#create-xmatters-ondemand-test-user)**  – du har en motsvarighet för Britta Simon i xMatters OnDemand som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med xMatters OnDemand:

1. I den [Azure-portalen](https://portal.azure.com/)på den **xMatters OnDemand** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![xMatters OnDemand domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [xMatters OnDemand klienten supportteam](https://www.xmatters.com/company/contact-us/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    > [!IMPORTANT]
    > Behöver du vidarebefordra certifikatet som den [xMatters OnDemand supportteam](https://www.xmatters.com/company/contact-us/). Certifikatet måste överföras av supportteamet xMatters innan du kan slutföra konfigurationen för enkel inloggning.

6. På den **konfigurera xMatters OnDemand** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurera xMatters OnDemand enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din XMatters OnDemand företagets webbplats som administratör.

2. I verktygsfältet högst upp, klickar du på **Admin**, och klicka sedan på **Company Details** i navigeringsfältet till vänster.

    ![Admin](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

3. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![SAML-konfiguration](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-konfiguration")

    a. Välj **Aktivera SAML**.

    b. I den **identitet Provider-ID** textrutan klistra in **Azure AD-identifierare** värde som du har kopierat från Azure-portalen.

    c. I den **inloggning på URL för enkel** textrutan klistra in **inloggnings-URL** värde som du har kopierat från Azure-portalen.

    d. I den **URL för enkel utloggning** textrutan klistra in **URL för utloggning**, som du har kopierat från Azure-portalen.

    e. På sidan Company Details överst, **spara ändringar**.

    ![Företagets information](./media/xmatters-ondemand-tutorial/IC776797.png "företagets information")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till xMatters OnDemand.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **xMatters OnDemand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **xMatters OnDemand**.

    ![XMatters OnDemand länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-xmatters-ondemand-test-user"></a>Skapa xMatters OnDemand testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i xMatters OnDemand.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din **XMatters OnDemand** klient.

2. Klicka på **användare** fliken och klicka sedan på **Lägg till användare**.

    ![Användare](./media/xmatters-ondemand-tutorial/IC781048.png "Användare")

3. I den **lägga till en användare** avsnittet, utför följande steg:

    ![Lägga till en användare](./media/xmatters-ondemand-tutorial/IC781049.png "lägga till en användare")

    a. Välj **Active** (Aktiv).

    b. I den **användar-ID** textrutan typ användar-id för användaren som Brittasimon@contoso.com.

    c. I textrutan för **förnamn** skriver du förnamnet på användaren, till exempel Britta.

    d. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    e. I den **plats** textrutan, ange den giltiga platsen i en giltig Azure AD-konto som du vill etablera.

    f. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på xMatters OnDemand-panelen i åtkomstpanelen bör det vara loggas in automatiskt till den xMatters OnDemand som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

