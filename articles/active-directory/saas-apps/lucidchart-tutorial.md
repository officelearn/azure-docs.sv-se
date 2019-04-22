---
title: 'Självstudier: Azure Active Directory-integrering med Lucidchart | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97bbb6b802f4a7a6378f283efd02cfb74873a903
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266825"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Självstudier: Azure Active Directory-integrering med Lucidchart

I den här självstudien får du lära dig hur du integrerar Lucidchart med Azure Active Directory (AD Azure).
Integrera Lucidchart med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Lucidchart.
* Du kan aktivera användarna att vara automatiskt inloggad till Lucidchart (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Lucidchart, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Lucidchart enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Lucidchart **SP** -initierad SSO
* Har stöd för Lucidchart **Just In Time** etableringen av användare

## <a name="adding-lucidchart-from-the-gallery"></a>Att lägga till Lucidchart från galleriet

För att konfigurera integrering av Lucidchart i Azure AD, som du behöver lägga till Lucidchart från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Lucidchart från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Lucidchart**väljer **Lucidchart** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Lucidchart i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Lucidchart baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Lucidchart upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Lucidchart, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Lucidchart Single Sign-On](#configure-lucidchart-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Lucidchart](#create-lucidchart-test-user)**  – du har en motsvarighet för Britta Simon i Lucidchart som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Lucidchart:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Lucidchart** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Lucidchart domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I den **inloggnings-URL** anger en URL som:  `https://chart2.office.lucidchart.com/saml/sso/azure`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På den **konfigurera Lucidchart** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-lucidchart-single-sign-on"></a>Konfigurera Lucidchart Single Sign-On

1. Logga in på webbplatsen Lucidchart företag som en administratör i ett annat webbläsarfönster.

2. Klicka på menyn längst upp **Team**.

    ![Team](./media/lucidchart-tutorial/ic791190.png "Team")

3. Klicka på **program \> hantera SAML**.

    ![Hantera SAML](./media/lucidchart-tutorial/ic791191.png "hantera SAML")

4. På den **SAML autentiseringsinställningar** dialogrutan utför följande steg:

    a. Välj **aktivera SAML-autentisering**, och klicka sedan på **valfritt**.

    ![SAML-autentiseringsinställningar](./media/lucidchart-tutorial/ic791192.png "SAML-autentiseringsinställningar")

    b. I den **domän** textrutan Skriv din domän och klicka på **ändra certifikat**.

    ![Ändra certifikat](./media/lucidchart-tutorial/ic791193.png "ändra certifikat")

    c. Öppna din hämtade metadatafilen, kopiera innehållet och klistra in den i den **överföra Metadata** textrutan.

    ![Ladda upp Metadata](./media/lucidchart-tutorial/ic791194.png "överföra Metadata")

    d. Välj **automatiskt lägga till nya användare till teamet**, och klicka sedan på **spara ändringar**.

    ![Spara ändringar](./media/lucidchart-tutorial/ic791195.png "Spara ändringar")

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Lucidchart.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Lucidchart**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Lucidchart**.

    ![Länken Lucidchart i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-lucidchart-test-user"></a>Skapa Lucidchart testanvändare

Det finns inga uppgift att konfigurera användaretablering för Lucidchart.  När en tilldelad användare försöker logga in med hjälp av åtkomstpanelen Lucidchart kontrollerar Lucidchart om användaren finns.  

Om det finns inget konto ännu, skapas den automatiskt av Lucidchart.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Lucidchart i åtkomstpanelen, bör det vara loggas in automatiskt till Lucidchart som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
