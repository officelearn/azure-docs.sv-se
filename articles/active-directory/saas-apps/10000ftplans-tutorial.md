---
title: 'Självstudier: Azure Active Directory-integrering med 10 000 fot planer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 10 000 fot planer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ef3c9d1fdbf030a820f2a3a1724bd18a4b0cedb
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501169"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Självstudier: Azure Active Directory-integrering med 10 000 fot planer

I den här självstudien får du lära dig hur du integrerar 10 000 fot planer med Azure Active Directory (AD Azure).
Integrera 10 000 fot planer med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till 10 000 fot planer.
* Du kan aktivera användarna att vara automatiskt inloggad till 10 000 fot planer (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med 10 000 fot planer, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* 10 000 fot planer enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* 10 000 fot planer support **SP** -initierad SSO
* 10 000 fot planer support **Just In Time** etableringen av användare

## <a name="adding-10000ft-plans-from-the-gallery"></a>Att lägga till 10 000 fot planer från galleriet

För att konfigurera integrering av 10 000 fot planer i Azure AD, som du behöver lägga till 10 000 fot planer från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till 10 000 fot planer från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program, klicka på den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **10 000 fot planer**väljer **10 000 fot planer** från panelen resultatet klickar på **Lägg till** för att lägga till programmet.

    ![10 000 fot planer i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med 10 000 fot ändras baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning att fungera, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i 10 000 fot planer måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med 10 000 fot planer, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera 10000ft planer enkel inloggning](#configure-10000ft-plans-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa 10000ft planer testanvändare](#create-10000ft-plans-test-user)**  – du har en motsvarighet för Britta Simon i 10 000 fot planer som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med 10 000 fot planer:

1. I den [Azure-portalen](https://portal.azure.com/)på den **10 000 fot planer** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![10 000 fot planer domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I den **inloggnings-URL** text skriver du URL: en: `https://app.10000ft.com`

    b. I den **identifierare (entitets-ID)** text skriver du URL: en: `https://app.10000ft.com/saml/metadata`

    > [!NOTE]
    > Värdet för **identifierare** ser annorlunda ut om du har en anpassad domän. Kontakta [10 000 fot planer klienten supportteamet](https://www.10000ft.com/plans/support) att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. På den **10 000 fot planera** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-10000ft-plans-single-sign-on"></a>Konfigurera enkel inloggning för 10000ft-planer

Att konfigurera enkel inloggning på **10 000 fot planer** sida, som du behöver skicka de hämtade **certifikat (Raw)** och lämpliga kopierade URL: er från Azure portal för att [10 000 fot planer support-teamet ](https://www.10000ft.com/plans/support). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** skriver `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till 10 000 fot planer.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **10 000 fot planer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **10 000 fot planer**.

    ![10 000 fot planer länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-10000ft-plans-test-user"></a>Skapa 10000ft planer testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i 10 000 fot planer. 10 000 fot planer stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i 10 000 fot planer, skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta den [10 000 fot planer klienten supportteamet](https://www.10000ft.com/plans/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen 10 000 fot planer i åtkomstpanelen, bör det vara loggas in automatiskt till 10 000 ft-planer som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
