---
title: 'Självstudier: Azure Active Directory-integrering med SensoScientific trådlösa temperatur övervakningssystemet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SensoScientific trådlösa temperatur av systemet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dc228f3473a4ddca8b5b68cdd99f1fced1a04cd
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922227"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Självstudier: Azure Active Directory-integrering med SensoScientific trådlösa temperatur av systemet

I den här självstudien får du lära dig hur du integrerar SensoScientific trådlösa temperatur av systemet med Azure Active Directory (AD Azure).
Integrera SensoScientific trådlösa temperatur av systemet med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SensoScientific trådlösa temperatur av systemet.
* Du kan aktivera användarna att vara automatiskt inloggad till SensoScientific trådlösa temperatur av systemet (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SensoScientific trådlösa temperatur av systemet, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SensoScientific trådlösa temperatur övervakningssystemet enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SensoScientific trådlösa temperatur av systemet **IDP** -initierad SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Att lägga till SensoScientific trådlösa temperatur av systemet från galleriet

För att konfigurera integrering av SensoScientific trådlösa temperatur av systemet i Azure AD, som du behöver lägga till SensoScientific trådlösa temperatur av systemet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SensoScientific trådlösa temperatur av systemet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SensoScientific trådlösa temperatur av systemet**väljer **SensoScientific trådlösa temperatur övervakningssystemet** resultatet panelen klickar **Lägg till**  för att lägga till programmet.

    ![SensoScientific trådlösa temperatur av systemet i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SensoScientific trådlösa temperatur av systemet baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SensoScientific trådlösa temperatur övervakningssystemet upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SensoScientific trådlösa temperatur av systemet, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SensoScientific trådlösa temperatur övervakning System enkel inloggning](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SensoScientific trådlösa temperatur övervakningssystemet testanvändare](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)**  – du har en motsvarighet för Britta Simon i SensoScientific trådlösa temperatur av systemet som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SensoScientific trådlösa temperatur av systemet:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SensoScientific trådlösa temperatur övervakningssystemet** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![SensoScientific trådlösa temperatur övervakning System domän och URL: er med enkel inloggning för information](common/preintegrated.png)

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera SensoScientific trådlösa temperatur övervakningssystemet** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurera SensoScientific trådlösa temperatur övervakning System enkel inloggning

1. Logga in på ditt övervakningssystem för SensoScientific trådlösa temperatur-program som administratör.

1. På överst navigeringsmenyn klickar du på **Configuration** och gå till **konfigurera** under **Single Sign On** att öppna på inställningar enkel inloggning och utför följande steg:

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Välj **Utfärdarens namn** som Azure AD.

    b. I den **utfärdar-URL** textrutan klistra in den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I den **enkel inloggnings-URL för** textrutan klistra in den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I den **tjänst-URL för enkel utloggning** textrutan klistra in den **URL för utloggning** som du har kopierat från Azure-portalen.

    e. Bläddra certifikatet som du har hämtat från Azure-portalen och laddar upp här.

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
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SensoScientific trådlösa temperatur av systemet.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SensoScientific trådlösa temperatur övervakningssystemet**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SensoScientific trådlösa temperatur övervakningssystemet**.

    ![Länken SensoScientific trådlösa temperatur av systemet i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Skapa SensoScientific trådlösa temperatur övervakningssystemet testanvändare

Om du vill aktivera Azure AD-användare att logga in på SensoScientific trådlösa temperatur av systemet, måste de etableras i SensoScientific trådlösa temperatur av systemet. Arbeta med [SensoScientific trådlösa temperatur övervakningssystemet supportteamet](https://www.sensoscientific.com/contact-us/) att lägga till användare i plattformen SensoScientific trådlösa temperatur av systemet. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SensoScientific trådlösa temperatur av systemet i åtkomstpanelen, bör det vara loggas in automatiskt till SensoScientific trådlösa temperatur övervakning av systemet som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

