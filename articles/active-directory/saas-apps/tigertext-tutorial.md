---
title: 'Självstudiekurs: Azure Active Directory-integrering med TigerText Secure Messenger | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088665"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Självstudiekurs: Azure Active Directory-integrering med TigerText Secure Messenger

I den här självstudien får du lära dig hur du integrerar TigerText Secure Messenger med Azure Active Directory (Azure AD).

Genom att integrera TigerText Secure Messenger med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TigerText Secure Messenger.
* Du kan aktivera dina användare så att de automatiskt loggas in på TigerText Secure Messenger (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med TigerText Secure Messenger behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En TigerText Secure Messenger-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du azure AD-enkel inloggning i en testmiljö och integrerar TigerText Secure Messenger med Azure AD.

TigerText Secure Messenger stöder SP-initierad enkel inloggning (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Lägg till TigerText Secure Messenger från Azure Marketplace

Om du vill konfigurera integreringen av TigerText Secure Messenger i Azure AD måste du lägga till TigerText Secure Messenger från Azure Marketplace i listan över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com?azure-portal=true).
1. Välj **Azure Active Directory** i den vänstra rutan.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **+ Nytt program** högst upp i fönstret.

    ![Alternativet Nytt program](common/add-new-app.png)

1. I sökrutan anger du **TigerText Secure Messenger**. I sökresultaten väljer du **TigerText Secure Messenger**och väljer sedan **Lägg till** för att lägga till programmet.

    ![TigerText Secure Messenger i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med TigerText Secure Messenger baserat på en testanvändare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och den relaterade användaren i TigerText Secure Messenger.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med TigerText Secure Messenger måste du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** så att användarna kan använda den här funktionen.
1. **[Konfigurera TigerText Secure Messenger enkel inloggning](#configure-tigertext-secure-messenger-single-sign-on)** för att konfigurera de enskilda inloggningsinställningarna på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** så att Britta Simon kan använda azure AD-enkel inloggning.
1. **[Skapa en TigerText Secure Messenger-testanvändare](#create-a-tigertext-secure-messenger-test-user)** så att det finns en användare som heter Britta Simon i TigerText Secure Messenger som är länkad till Azure AD-användaren Britta Simon.
1. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med TigerText Secure Messenger:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **TigerText Secure** **Messenger-programintegration**.

    ![Konfigurera alternativet enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. Öppna fönstret **Grundläggande SAML-konfiguration** i fönstret **Edit** **Konfigurera enkel inloggning med SAML.**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Gör följande i fönstret **Grundläggande SAML-konfiguration:**

    ![TigerText Secure Messenger-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    1. Ange en WEBBADRESS i rutan **Logga in på WEBBADRESS:**

       `https://home.tigertext.com`

    1. I rutan **Identifierare (Enhetsenhets-ID)** skriver du en URL med hjälp av följande mönster:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > **Värdet identifierare (entitets-ID)** är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Kontakta Supportteamet för [TigerText Secure Messenger](mailto:prosupport@tigertext.com)för att få värdet . Du kan också referera till de mönster som visas i fönstret **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I fönstret **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du **Hämta** för att hämta **XML-koden för federationsmetadata** från de angivna alternativen och spara den på datorn.

    ![Alternativet XML-hämtning av federationsmetadata](common/metadataxml.png)

1. I avsnittet **Konfigurera TigerText Secure Messenger** kopierar du webbadressen eller webbadresserna som du behöver:

   * **Inloggnings-URL**
   * **Azure AD-identifierare**
   * **Utloggnings-URL**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurera TigerText Secure Messenger enkel inloggning

Om du vill konfigurera enkel inloggning på TigerText Secure Messenger-sidan måste du skicka den nedladdade XML-koden för federationsmetadata och lämpliga kopierade URL:er från Azure-portalen till Supportteamet för [TigerText Secure Messenger](mailto:prosupport@tigertext.com). TigerText Secure Messenger-teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge dem åtkomst till TigerText Secure Messenger.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** > **TigerText Secure Messenger**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **TigerText Secure Messenger**i programlistan .

    ![TigerText Secure Messenger i programlistan](common/all-applications.png)

1. Välj **Användare och grupper**under **HANTERA**i den vänstra rutan.

    ![Alternativet "Användare och grupper"](common/users-groups-blade.png)

1. Välj **+ Lägg till användare**och välj sedan Användare och **grupper** i fönstret Lägg **till tilldelning.**

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **Användare och grupper** väljer du **Britta Simon** i listan **Användare** och väljer sedan **Välj** längst ned i fönstret.

1. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i fönstret **Välj roll.** **Välj**längst ned i fönstret .

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Skapa en TigerText Secure Messenger-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i TigerText Secure Messenger. Arbeta med Supportteamet för [TigerText Secure Messenger](mailto:prosupport@tigertext.com) för att lägga till Britta Simon som användare i TigerText Secure Messenger. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du väljer **TigerText Secure Messenger** i portalen Mina appar ska du automatiskt loggas in på den TigerText Secure Messenger-prenumeration som du konfigurerar enkel inloggning för. Mer information om portalen Mina appar finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för integrering av SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
