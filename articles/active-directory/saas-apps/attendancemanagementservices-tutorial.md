---
title: 'Självstudiekurs: Azure Active Directory-integrering med Närvarohanteringstjänster | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Närvarohanteringstjänster.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143d0afce7a3644286703a9eba0da1ee45305f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67106549"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Självstudiekurs: Azure Active Directory-integrering med Närvarohanteringstjänster

I den här självstudien får du lära dig hur du integrerar Närvarohanteringstjänster med Azure Active Directory (Azure AD).
Genom att integrera närvarohanteringstjänster med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Närvarohanteringstjänster.
* Du kan aktivera dina användare så att de automatiskt loggas in på Närvarohanteringstjänster (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Närvarohanteringstjänster behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Deltagande Management Services enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Närvaro Management Services stöder **SP** initierade SSO

## <a name="adding-attendance-management-services-from-the-gallery"></a>Lägga till närvarohanteringstjänster från galleriet

Om du vill konfigurera integreringen av Närvarohanteringstjänster i Azure AD måste du lägga till Närvarohanteringstjänster från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Närvarohanteringstjänster från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Närvarohanteringstjänster**i sökrutan och välj **Närvarohanteringstjänster** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Närvarohanteringstjänster i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Närvarohanteringstjänster baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Närvarohanteringstjänster upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Närvarohanteringstjänster måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera närvarohanteringstjänster enkel inloggning](#configure-attendance-management-services-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa närvarohanteringstjänster testanvändare](#create-attendance-management-services-test-user)** - om du vill ha en motsvarighet till Britta Simon i Närvarohanteringstjänster som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Närvarohanteringstjänster:

1. Välj Enkel inloggning på sidan **Närvarohanteringstjänster** i [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Domän för närvarohanteringstjänster och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://id.obc.jp/<tenant information >/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Närvarohanteringstjänster Klientsupportteam](https://www.obcnet.jp/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera närvarohanteringstjänster** kopierar du lämpliga webbadresser enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Konfigurera enstaka inloggningstjänster för närvarohanteringstjänster

1. I ett annat webbläsarfönster loggar du in på webbplatsen för närvarohanteringstjänster som administratör.

1. Klicka på **SAML-autentisering** under **avsnittet Säkerhetshantering**.

    ![Konfiguration av närvarohanteringstjänster](./media/attendancemanagementservices-tutorial/user1.png)

1. Utför följande steg:

    ![Konfiguration av närvarohanteringstjänster](./media/attendancemanagementservices-tutorial/user2.png)

    a. Välj **Använd SAML-autentisering**.

    b. Klistra in värdet för **Azure AD Identifier-värde** i textrutan **Identifierare** som du har kopierat från Azure-portalen.

    c. Klistra in värdet för **url-värdet för inloggnings-URL** som du har kopierat från Azure-portalen i url-textrutan för **autentisering.**

    d. Klicka på **Välj en fil** om du vill ladda upp certifikatet som du hämtade från Azure AD.

    e. Välj **Inaktivera lösenordsautentisering**.

    f. Klicka på **Registrering**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Närvarohanteringstjänster.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Närvarohanteringstjänster**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Närvarohanteringstjänster**i programlistan .

    ![Länken Närvarohanteringstjänster i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-attendance-management-services-test-user"></a>Skapa testanvändare för närvarohanteringstjänster

Om du vill att Azure AD-användare ska kunna logga in på Närvarohanteringstjänster måste de etableras i Närvarohanteringstjänster. När det gäller Närvarohanteringstjänster är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på webbplatsen för närvarohanteringstjänster som administratör.

1. Klicka på **Användarhantering** under **avsnittet Säkerhetshantering**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user5.png)

1. Klicka på **Logga in nya regler**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user3.png)

1. Gör följande i avsnittet **OBCiD-information:**

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user4.png)

    a. Skriv **OBCiD** e-postmeddelandet för användaren som `BrittaSimon\@contoso.com`.

    b. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    c. Klicka på **Registrering**

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Närvarohanteringstjänster på åtkomstpanelen ska du automatiskt loggas in på de närvarohanteringstjänster som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)