---
title: 'Självstudier: Azure Active Directory-integrering med ScreenSteps | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 570f789d0f399c5ffa7535101136ab65ba58ccd5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277093"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Självstudier: Azure Active Directory-integrering med ScreenSteps

I den här självstudien får du lära dig hur du integrerar ScreenSteps med Azure Active Directory (AD Azure).
Integrera ScreenSteps med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ScreenSteps.
* Du kan aktivera användarna att vara automatiskt inloggad till ScreenSteps (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ScreenSteps, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ScreenSteps enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för ScreenSteps **SP** -initierad SSO

## <a name="adding-screensteps-from-the-gallery"></a>Att lägga till ScreenSteps från galleriet

För att konfigurera integrering av ScreenSteps i Azure AD, som du behöver lägga till ScreenSteps från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ScreenSteps från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **ScreenSteps**väljer **ScreenSteps** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![ScreenSteps i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ScreenSteps baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ScreenSteps upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ScreenSteps, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ScreenSteps Single Sign-On](#configure-screensteps-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare ScreenSteps](#create-screensteps-test-user)**  – du har en motsvarighet för Britta Simon i ScreenSteps som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med ScreenSteps:

1. I den [Azure-portalen](https://portal.azure.com/)på den **ScreenSteps** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ScreenSteps domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera det här värdet med det faktiska inloggnings-URL, vilket beskrivs senare i den här självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera ScreenSteps** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-screensteps-single-sign-on"></a>Konfigurera ScreenSteps Single Sign-On

1. Logga in på webbplatsen ScreenSteps företag som en administratör i ett annat webbläsarfönster.

1. Klicka på **kontoinställningar**.

    ![Kontohantering](./media/screensteps-tutorial/ic778523.png "kontohantering")

1. Klicka på **Enkel inloggning**.

    ![Fjärrautentiseringen](./media/screensteps-tutorial/ic778524.png "fjärrautentiseringen")

1. Klicka på **skapa slutpunkt för enkel inloggning**.

    ![Fjärrautentiseringen](./media/screensteps-tutorial/ic778525.png "fjärrautentiseringen")

1. I den **slutpunkt för skapa enkel inloggning** avsnittet, utför följande steg:

    ![Skapa en autentiseringsslutpunkt](./media/screensteps-tutorial/ic778526.png "skapa en autentiseringsslutpunkt")

    a. I den **rubrik** textrutan anger du ett rubrik.

    b. Från den **läge** väljer **SAML**.

    c. Klicka på **Skapa**.

1. **Redigera** den nya slutpunkten.

    ![Redigera slutpunkten](./media/screensteps-tutorial/ic778528.png "redigera slutpunkten")

1. I den **slutpunkt för Redigera enkel inloggning** avsnittet, utför följande steg:

    ![Remote autentiseringsslutpunkt](./media/screensteps-tutorial/ic778527.png "Remote autentiseringsslutpunkt")

    a. Klicka på **överför nya SAML-certifikatet fil**, och sedan ladda upp certifikatet som du har hämtat från Azure-portalen.

    b. Klistra in **inloggnings-URL** värde, som du har kopierat från Azure-portalen till den **Remote inloggnings-URL** textrutan.

    c. Klistra in **URL för utloggning** värde, som du har kopierat från Azure-portalen till den **URL för utloggning** textrutan.

    d. Välj en **grupp** att tilldela användare till när de har etablerats.

    e. Klicka på **Uppdatera**.

    f. Kopiera den **URL för SAML-konsument** till Urklipp och klistra in till den **inloggnings-URL** -textrutan i **SAML grundkonfiguration** avsnitt i Azure-portalen.

    g. Gå tillbaka till den **redigera slutpunkt för enkel inloggning**.

    h. Klicka på den **ange som standard för kontot** knappen för att använda den här slutpunkten för alla användare som loggar in på ScreenSteps. Du kan också klicka på den **lägga till sidan** knappen för att använda den här slutpunkten för specifika platser i **ScreenSteps**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ScreenSteps.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **ScreenSteps**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **ScreenSteps**.

    ![Länken ScreenSteps i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-screensteps-test-user"></a>Skapa ScreenSteps testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i ScreenSteps. Arbeta med [ScreenSteps klienten supportteamet](https://www.screensteps.com/contact) att lägga till användare i ScreenSteps-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ScreenSteps i åtkomstpanelen, bör det vara loggas in automatiskt till ScreenSteps som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)