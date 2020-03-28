---
title: 'Självstudiekurs: Azure Active Directory-integrering med Uberflip | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048479"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Självstudiekurs: Azure Active Directory-integrering med Uberflip

I den här självstudien får du lära dig hur du integrerar Uberflip med Azure Active Directory (Azure AD).

Genom att integrera Uberflip med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Uberflip.
* Du kan aktivera dina användare så att de automatiskt loggas in på Uberflip (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Uberflip behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En Uberflip-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

Uberflip stöder följande funktioner:

* SP-initierad och IDP-initierad enkel inloggning (SSO).
* Just-in-time-användaretablering.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Lägga till Uberflip från Azure Marketplace

Om du vill konfigurera integreringen av Uberflip i Azure AD måste du lägga till Uberflip från Azure Marketplace i listan över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory** i den vänstra rutan.

   ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

   ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ Nytt program** högst upp i fönstret.

   ![Alternativet Nytt program](common/add-new-app.png)

1. Ange **Uberflip**i sökrutan . I sökresultaten väljer du **Uberflip**och väljer sedan **Lägg till** för att lägga till programmet.

   ![Uberflip i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Uberflip baserat på en testanvändare med namnet **B Simon**. För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i Uberflip.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Uberflip måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera Uberflip enkel inloggning](#configure-uberflip-single-sign-on)** för att konfigurera de enskilda inloggningsinställningarna på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B. Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B. Simon för att använda Azure AD enkel inloggning.
1. **[Skapa en Uberflip-testanvändare](#create-an-uberflip-test-user)** så att det finns en användare som heter B. Simon i Uberflip som är länkad till Azure AD-användaren B. Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Uberflip:

1. Välj Enkel inloggning på sidan Uberflip-programintegrering på [Azure-portalen](https://portal.azure.com/). **Uberflip** **Single sign-on**

    ![Konfigurera alternativet enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. Öppna fönstret **Grundläggande SAML-konfiguration** i fönstret **Edit** **Konfigurera enkel inloggning med SAML.**

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Gör något av följande i fönstret **Grundläggande SAML-konfiguration,** beroende på vilket SSO-läge du vill konfigurera:

   * Om du vill konfigurera programmet i IDP-initierat SSO-läge anger du en URL i rutan **Svars-URL (Url för kontrolltjänster för konsumenttjänster) i rutan Svars-URL(Kontroll konsumenttjänsts URL)** med hjälp av följande mönster:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip-domän och webbadresser med enkel inloggning](common/both-replyurl.png)

     > [!NOTE]
     > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Kontakta [Uberflip-supportteamet](mailto:support@uberflip.com)för att få det faktiska värdet . Du kan också referera till de mönster som visas i fönstret **Grundläggande SAML-konfiguration** i Azure-portalen.

   * Om du vill konfigurera programmet i SP-initierat SSO-läge väljer du **Ange ytterligare webbadresser**och anger den här URL:en i rutan **Sign-on-URL:**

     `https://app.uberflip.com/users/login`

     ![Uberflip-domän och webbadresser med enkel inloggning](common/both-signonurl.png)

1. I fönstret **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du **Hämta** för att hämta **XML-koden för federationsmetadata** från de angivna alternativen och spara den på datorn.

   ![Alternativet XML-hämtning av federationsmetadata](common/metadataxml.png)

1. Kopiera webbadressen eller webbadresserna som du behöver i fönstret **Konfigurera Uberflip:**

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurera enkel inloggning för Uberflip

Om du vill konfigurera enkel inloggning på Uberflip-sidan måste du skicka den nedladdade XML-koden för federationsmetadata och lämpliga kopierade URL:er från Azure-portalen till [Uberflip-supportteamet](mailto:support@uberflip.com). Uberflip-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet B. Simon i Azure-portalen.

1. I Den vänstra rutan i Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

    ![Alternativen Användare och "Alla användare"](common/users.png)

1. Högst upp på skärmen väljer du **+ Ny användare**.

    ![Alternativet Ny användare](common/new-user.png)

1. Gör följande i **användarfönstret:**

    ![Användarfönstret](common/user-properties.png)

    1. Ange **BSimon**i rutan **Namn** .
  
    1. I rutan **Användarnamn** anger du **BSimon\@\<ditt företag i>.\< förlängning>**. Till exempel **contoso.com\@BSimon**.

    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera B. Simon för att använda Azure enkel inloggning genom att ge dem åtkomst till Uberflip.

1. I Azure-portalen väljer du **Enterprise Applications** > **Alla program** > **Uberflip**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **Uberflip**i programlistan .

    ![Uberflip i programlistan](common/all-applications.png)

1. Välj **Användare och grupper**under **HANTERA**i den vänstra rutan.

    ![Alternativet "Användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan Användare och **grupper** i fönstret Lägg **till tilldelning.**

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **Användare och grupper** väljer du B **Simon** i listan **Användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i fönstret **Välj roll.** **Välj**längst ned i fönstret .

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-an-uberflip-test-user"></a>Skapa en Uberflip-testanvändare

En användare med namnet B. Simon skapas nu i Uberflip. Du behöver inte göra något för att skapa den här användaren. Uberflip stöder just-in-time-användaretablering, vilket är aktiverat som standard. Om en användare med namnet B. Simon inte redan finns i Uberflip skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [Supportteamet för Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du väljer **Uberflip** i portalen Mina appar ska du automatiskt loggas in på uberflip-prenumerationen som du konfigurerar enkel inloggning för. Mer information om portalen Mina appar finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för integrering av SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
