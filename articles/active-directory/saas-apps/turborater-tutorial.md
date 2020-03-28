---
title: 'Självstudiekurs: Azure Active Directory-integrering med TurboRater | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088280"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Självstudiekurs: Azure Active Directory-integrering med TurboRater

I den här självstudien får du lära dig hur du integrerar TurboRater med Azure Active Directory (Azure AD).

Genom att integrera TurboRater med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TurboRater.
* Du kan aktivera dina användare så att de automatiskt loggas in på TurboRater (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med TurboRater behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En TurboRater-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

TurboRater stöder IDP-initierad enkel inloggning (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Lägg till TurboRater från Azure Marketplace

Om du vill konfigurera integreringen av TurboRater i Azure AD måste du lägga till TurboRater från Azure Marketplace i listan över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Alternativet Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ Nytt program** högst upp i fönstret.

    ![Alternativet Nytt program](common/add-new-app.png)

1. Ange **TurboRater**i sökrutan . I sökresultaten väljer du **TurboRater**och väljer sedan **Lägg till** för att lägga till programmet.

    ![TurboRater i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med TurboRater baserat på en testanvändare med namnet **B Simon**. För enkel inloggning för att fungera måste du upprätta en länk mellan en Azure AD-användare och den relaterade användaren i TurboRater.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med TurboRater måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera TurboRater enkel inloggning](#configure-turborater-single-sign-on)** för att konfigurera de enskilda inloggningsinställningarna på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B. Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B. Simon för att använda Azure AD enkel inloggning.
1. **[Skapa en TurboRater-testanvändare](#create-a-turborater-test-user)** så att det finns en användare som heter B. Simon i TurboRater som är länkad till Azure AD-användaren B. Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med TurboRater:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **TurboRater-programintegration** . **Single sign-on**

    ![Konfigurera alternativet enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. På sidan Konfigurera enkel inloggning med SAML väljer du **Redigera** (pennikonen) för att öppna fönstret **Grundläggande SAML-konfiguration.** **Set up Single Sign-On with SAML**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Gör följande i fönstret **Grundläggande SAML-konfiguration:**

    ![TurboRater-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    1. Ange en URL i rutan **Identifierare (enhets-ID):**

       `https://www.itcdataservices.com`

    1. I rutan **Svarsadress (Url för kontroll av konsumenttjänst)** anger du en URL med hjälp av följande mönster:

       | Miljö | URL |
       | ---------------| --------------- |
       | Testa  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Live  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Kontakta [TurboRater-supportteamet](https://www.getitc.com/support)för att få dessa värden. Du kan också referera till de mönster som visas i fönstret **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I fönstret **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du **Hämta** för att hämta **XML-koden för federationsmetadata** från de angivna alternativen och spara den på datorn.

    ![Alternativet XML-hämtning av federationsmetadata](common/metadataxml.png)

1. Kopiera webbadressen eller webbadresserna som du behöver i avsnittet **Konfigurera TurboRater:**

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurera enkel inloggning för TurboRater

Om du vill konfigurera enkel inloggning på TurboRater-sidan måste du skicka den nedladdade XML-koden för federationsmetadata och lämpliga kopierade URL:er från Azure-portalen till [TurboRater-supportteamet](https://www.getitc.com/support). TurboRater-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**   > **Users** > **All users**.

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

I det här avsnittet kan du aktivera B. Simon för att använda Azure enkel inloggning genom att bevilja deras åtkomst till TurboRater.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** > **TurboRater**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **TurboRater**i programlistan .

    ![TurboRater i programlistan](common/all-applications.png)

1. Välj **Användare och grupper**under **HANTERA**i den vänstra rutan.

    ![Alternativet "Användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan Användare och **grupper** i fönstret Lägg **till tilldelning.**

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **Användare och grupper** väljer du **B. Simon** i listan **Användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i fönstret **Välj roll.** **Välj**längst ned i fönstret .

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-a-turborater-test-user"></a>Skapa en TurboRater-testanvändare

I det här avsnittet skapar du en användare som heter B. Simon i TurboRater. Arbeta med [TurboRater-supportteamet](https://www.getitc.com/support) för att lägga till B. Simon som användare i TurboRater. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du väljer **TurboRater** i portalen Mina appar ska du automatiskt loggas in på den TurboRater-prenumeration som du konfigurerar enkel inloggning för. Mer information om portalen Mina appar finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för integrering av SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
