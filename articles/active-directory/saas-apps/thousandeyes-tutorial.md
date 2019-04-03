---
title: 'Självstudier: Azure Active Directory-integrering med ThousandEyes | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 605ab73a11874c85f20728f5524eb770a7a1b259
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848006"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Självstudier: Azure Active Directory-integrering med ThousandEyes

I den här självstudien får du lära dig hur du integrerar ThousandEyes med Azure Active Directory (AD Azure).
Integrera ThousandEyes med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ThousandEyes.
* Du kan aktivera användarna att vara automatiskt inloggad till ThousandEyes (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ThousandEyes, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ThousandEyes enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för ThousandEyes **SP** -initierad SSO

* Har stöd för ThousandEyes [ **automatisk** etableringen av användare](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

## <a name="adding-thousandeyes-from-the-gallery"></a>Att lägga till ThousandEyes från galleriet

För att konfigurera integrering av ThousandEyes i Azure AD, som du behöver lägga till ThousandEyes från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ThousandEyes från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **ThousandEyes**väljer **ThousandEyes** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![ThousandEyes i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ThousandEyes baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ThousandEyes upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ThousandEyes, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ThousandEyes Single Sign-On](#configure-thousandeyes-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare ThousandEyes](#create-thousandeyes-test-user)**  – du har en motsvarighet för Britta Simon i ThousandEyes som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med ThousandEyes:

1. I den [Azure-portalen](https://portal.azure.com/)på den **ThousandEyes** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ThousandEyes domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://app.thousandeyes.com/login/sso`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera ThousandEyes** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-thousandeyes-single-sign-on"></a>Konfigurera ThousandEyes Single Sign-On

1. I ett annat webbläsarfönster, loggar du in på ditt **ThousandEyes** företagets plats som administratör.

2. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/thousandeyes-tutorial/ic790066.png "Inställningar")

3. Klicka på **konto**

    ![Kontot](./media/thousandeyes-tutorial/ic790067.png "konto")

4. Klicka på den **säkerhet och autentisering** fliken.

    ![Säkerhet och autentisering](./media/thousandeyes-tutorial/ic790068.png "säkerhet och autentisering")

5. I den **installationsprogrammet Single Sign-On** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/ic790069.png "Konfigurera enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. I **inloggnings-URL för sidan** textrutan klistra in **inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. I **sid-URL för utloggning** textrutan klistra in **URL för utloggning**, som du har kopierat från Azure-portalen.

    d. **Providerutgivare** textrutan klistra in **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    e. I **verifieringscertifikatet**, klickar du på **Välj fil**, och sedan ladda upp det certifikat som du har hämtat från Azure-portalen.

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
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ThousandEyes.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **ThousandEyes**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **ThousandEyes**.

    ![Länken ThousandEyes i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-thousandeyes-test-user"></a>Skapa ThousandEyes testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ThousandEyes. ThousandEyes stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information [här](thousandeyes-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på webbplatsen ThousandEyes företag som administratör.

2. Klicka på **Inställningar**.

    ![Inställningar](./media/thousandeyes-tutorial/IC790066.png "Inställningar")

3. Klicka på **Konto**.

    ![Kontot](./media/thousandeyes-tutorial/IC790067.png "konto")

4. Klicka på den **konton och användare** fliken.

    ![Konton och användare](./media/thousandeyes-tutorial/IC790073.png "konton och användare")

5. I den **lägga till användare och konton** avsnittet, utför följande steg:

    ![Lägga till användarkonton](./media/thousandeyes-tutorial/IC790074.png "lägga till användarkonton")

    a. I **namn** textrutan skriver du namnet på användaren som **Britta Simon**.

    b. I **e-post** textrutan typ e-postmeddelandet av användare som brittasimon@contoso.com.

    b. Klicka på **lägga till nya användare till kontot**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavare får ett e-postmeddelande, inklusive en länk för att bekräfta och aktivera kontot.

> [!NOTE]
> Du kan använda alla andra ThousandEyes användare konto verktyg för att skapa eller API: er som tillhandahålls av ThousandEyes för att etablera Azure Active Directory användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ThousandEyes i åtkomstpanelen, bör det vara loggas in automatiskt till ThousandEyes som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)
