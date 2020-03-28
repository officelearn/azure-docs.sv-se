---
title: 'Självstudiekurs: Azure Active Directory-integrering med PageDNA | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68227470"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Självstudiekurs: Azure Active Directory-integrering med PageDNA

I den här självstudien får du lära dig hur du integrerar PageDNA med Azure Active Directory (Azure AD).

Genom att integrera PageDNA med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till PageDNA.
* Du kan aktivera dina användare så att de automatiskt loggas in på PageDNA (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med PageDNA behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En PageDNA-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du azure AD-enkel inloggning i en testmiljö och integrerar PageDNA med Azure AD.

PageDNA stöder följande funktioner:

* SP-initierad enkel inloggning (SSO).

* Just-in-time-användaretablering.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Lägga till PageDNA från Azure Marketplace

Om du vill konfigurera integreringen av PageDNA i Azure AD måste du lägga till PageDNA från Azure Marketplace i listan över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ Nytt program** högst upp i fönstret.

    ![Alternativet Nytt program](common/add-new-app.png)

1. Skriv **PageDNA**i sökrutan . I sökresultaten väljer du **PageDNA**och väljer sedan **Lägg till** för att lägga till programmet.

    ![PageDNA i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med PageDNA baserat på en testanvändare som heter **Britta Simon**. För enkel inloggning för att fungera måste du upprätta en länk mellan en Azure AD-användare och den relaterade användaren i PageDNA.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med PageDNA måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera PageDNA enkel inloggning](#configure-pagedna-single-sign-on)** för att konfigurera de enskilda inloggningsinställningarna på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** så att Britta Simon kan använda azure AD-enkel inloggning.
1. **[Skapa en PageDNA-testanvändare](#create-a-pagedna-test-user)** så att det finns en användare som heter Britta Simon i PageDNA som är länkad till Azure AD-användaren Britta Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med PageDNA:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **PageDNA-programintegration** . **Single sign-on**

    ![Konfigurera alternativet enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. Öppna fönstret **Grundläggande SAML-konfiguration** i fönstret **Edit** **Konfigurera enkel inloggning med SAML.**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Gör följande i fönstret **Grundläggande SAML-konfiguration:**

    ![PageDNA-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    1. Ange en WEBBADRESS i rutan **Logga in på WEBBADRESS** med något av följande mönster:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. I rutan **Identifierare (Enhetsenhets-ID)** anger du en URL med något av följande mönster:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [PageDNA-supportteamet](mailto:success@pagedna.com)för att hämta dessa värden . Du kan också referera till de mönster som visas i fönstret **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I fönstret **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du **Hämta** för att hämta **certifikat (Raw)** från de angivna alternativen och spara det på datorn.

    ![Hämta certifikat (Raw)](common/certificateraw.png)

1. Kopiera webbadressen eller webbadresserna som du behöver i avsnittet **Konfigurera PageDNA:**

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurera PageDNA enkel inloggning

Om du vill konfigurera enkel inloggning på PageDNA-sidan skickar du det hämtade certifikatet (Raw) och lämpliga kopierade URL:er från Azure-portalen till [PageDNA-supportteamet](mailto:success@pagedna.com). PageDNA-teamet kontrollerar att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen med namnet Britta Simon.

1. I Den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**   > **Users** > **All users**.

    ![Alternativen Användare och "Alla användare"](common/users.png)

1. Högst upp på skärmen väljer du **+ Ny användare**.

    ![Alternativet Ny användare](common/new-user.png)

1. Gör följande i **användarfönstret:**

    ![Användarfönstret](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **Användarnamn** anger du **BrittaSimon\@\<ditt\< företag>. förlängning>**. **Till exempel,\@BrittaSimon contoso.com**.

    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera användaren Britta Simon att använda Azure enkel inloggning genom att ge användaren åtkomst till PageDNA.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** > **PageDNA**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **PageDNA**i programlistan .

    ![PageDNA i programlistan](common/all-applications.png)

1. Välj **Användare och grupper**under **HANTERA**i den vänstra rutan.

    ![Alternativet "Användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan Användare och **grupper** i fönstret Lägg **till tilldelning.**

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **Användare och grupper** väljer du **Britta Simon** i listan **Användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i fönstret **Välj roll.** **Välj**längst ned i fönstret .

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-a-pagedna-test-user"></a>Skapa en PageDNA-testanvändare

En användare vid namn Britta Simon skapas nu i PageDNA. Du behöver inte göra något för att skapa den här användaren. PageDNA stöder just-in-time-användaretablering, vilket är aktiverat som standard. Om en användare med namnet Britta Simon inte redan finns i PageDNA skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du väljer **PageDNA** i portalen Mina appar ska du automatiskt loggas in på den PageDNA-prenumeration som du konfigurerar enkel inloggning för. Mer information om portalen Mina appar finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för integrering av SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

