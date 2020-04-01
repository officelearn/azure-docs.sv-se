---
title: 'Självstudiekurs: Azure Active Directory-integrering med 10 000 ft abonnemang | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 10 000 ft-abonnemang.
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
ms.openlocfilehash: 441c9345f92974c387429baac768dbccb03fc995
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107608"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Självstudiekurs: Azure Active Directory-integrering med 10 000 ft abonnemang

I den här självstudien får du lära dig hur du integrerar 10 000 ft-abonnemang med Azure Active Directory (Azure AD).
Genom att integrera 10 000 ft-abonnemang med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till 10 000 ft abonnemang.
* Du kan aktivera dina användare så att de automatiskt loggas in på 10 000 ft abonnemang (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med 10 000 ft abonnemang behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* 10 000 ft Abonnemang med enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* 10,000ft Planer stöd **SP** initierade SSO
* 10,000ft Planer stöd **Just In Time** användare etablering

## <a name="adding-10000ft-plans-from-the-gallery"></a>Lägga till 10.000 ft Planer från galleriet

Om du vill konfigurera integreringen av 10 000 ft-abonnemang i Azure AD måste du lägga till 10 000 ft abonnemang från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till 10 000 ft abonnemang från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **10 000 ft Planer**i sökrutan och välj **10 000 ft Abonnemang** på resultatpanelen och klicka sedan på knappen **Lägg** till för att lägga till programmet.

    ![10.000 ft Planer i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med 10 000 ft abonnemang baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i 10 000 ft-abonnemang upprättas.

Om du vill konfigurera och testa en enda Azure AD-inloggning med 10 000 ft-abonnemang måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera 10000ft Planer Enkel inloggning](#configure-10000ft-plans-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa 10000ft Planer testanvändare](#create-10000ft-plans-test-user)** - att ha en motsvarighet till Britta Simon i 10.000 ft Planer som är kopplad till Azure AD representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enda Azure AD-inloggning med 10 000 ft abonnemang:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**på sidan **10 000 ft Planer** programintegration .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![10 000 ft Planer Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://app.10000ft.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://app.10000ft.com/saml/metadata`

    > [!NOTE]
    > Värdet för **Identifierare** är annorlunda om du har en anpassad domän. Kontakta [10 000 ft Abonnemang Klientsupportteam](https://www.10000ft.com/plans/support) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. Kopiera lämpliga webbadresser enligt dina behov i avsnittet **Konfigurera 10 000 ft abonnemang.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-10000ft-plans-single-sign-on"></a>Konfigurera 10000ft planer enkel inloggning

Om du vill konfigurera enkel inloggning på **10 000 ft-abonnemangssidan** måste du skicka det nedladdade **certifikatet (Raw)** och lämpliga kopierade url:er från Azure-portalen till [10 000 ft supportteam för abonnemang](https://www.10000ft.com/plans/support). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. Ange **BrittaSimon**i fältet **Namn** .
  
    b. Skriv `brittasimon@yourcompanydomain.extension`i fältet **Användarnamn** . Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till 10 000 ft-abonnemang.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **10 000 ft abonnemang**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **10 000 ft abonnemang**i programlistan .

    ![Länken 10 000 ft Planer i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-10000ft-plans-test-user"></a>Skapa 10000ft Planer testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i 10 000 ft-abonnemang. 10,000ft Planer stöder just-in-time användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i 10 000 ft-abonnemang skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta supportteamet för [10 000 ft abonnemangsklient](https://www.10000ft.com/plans/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen 10 000 ft abonnemang på åtkomstpanelen bör du automatiskt loggas in på de 10 000 ft-abonnemang som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
