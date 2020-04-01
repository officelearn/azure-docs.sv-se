---
title: 'Självstudiekurs: Azure Active Directory-integrering med ThirdLight | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ThirdLight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 448d46cd21a63488c4f567d5555fe6406fc0fa73
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089097"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Självstudiekurs: Azure Active Directory-integrering med ThirdLight

I den här självstudien får du lära dig hur du integrerar ThirdLight med Azure Active Directory (Azure AD). Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till ThirdLight.
* Du kan aktivera dina användare så att de automatiskt loggas in på ThirdLight (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Om du vill veta mer om Integrering av SaaS-appar med Azure AD läser du [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med ThirdLight måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En ThirdLight-prenumeration som har enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* ThirdLight stöder SP-initierad SSO.

## <a name="add-thirdlight-from-the-gallery"></a>Lägg till ThirdLight från galleriet

Om du vill konfigurera integreringen av ThirdLight i Azure AD måste du lägga till ThirdLight från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program:**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Skriv **ThirdLight**i sökrutan . Välj **ThirdLight** i sökresultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med ThirdLight med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i ThirdLight.

Om du vill konfigurera och testa azure AD-enkel inloggning med ThirdLight måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera ThirdLight enkel inloggning på](#configure-thirdlight-single-sign-on)** programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en ThirdLight-testanvändare](#create-a-thirdlight-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med ThirdLight:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan ThirdLight-programintegration: **Single sign-on**

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. Gör följande i dialogrutan **Grundläggande SAML-konfiguration.**

    ![Dialogrutan Grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. Ange en WEBBADRESS i det här mönstret i rutan **Logga in på webbadress:**
    
          `https://<subdomain>.thirdlight.com/`

    1. Ange en URL i det här mönstret i rutan **Identifierare (entitets-ID):**

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Dessa värden är platshållare. Du måste använda den faktiska inloggnings-URL:en och identifieraren. Kontakta [Supportteamet](https://www.thirdlight.com/support) för ThirdLight för att få värdena. Du kan också referera till de mönster som visas i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du länken **Hämta bredvid** **FEDERATION Metadata XML**enligt dina behov och sparar filen på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera ThirdLight** kopierar du lämpliga webbadresser baserat på dina krav:

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurera tredjelight-inloggning

1. Logga in på din ThirdLight-företagswebbplats som administratör i ett nytt webbläsarfönster.

1. Gå till **CONFIGURATION** > **System Administration** > **SAML2:**

    ![Systemadministration](./media/thirdlight-tutorial/ic805843.png "Systemadministration")

1. I konfigurationsavsnittet FÖR SAML2 gör du följande steg.
  
    ![Konfigurationsavsnittet FÖR SAML2](./media/thirdlight-tutorial/ic805844.png "Konfigurationsavsnittet FÖR SAML2")

    1. Välj **Aktivera SAML2 enkel inloggning**.

    1. Under **Källa för IdP-metadata**väljer du **Läs in IdP-metadata från XML**.

    1. Öppna metadatafilen som du hämtade från Azure-portalen i föregående avsnitt. Kopiera filens innehåll och klistra in den i **XML-rutan för IdP-metadata.**

    1. Välj **Spara SAML2-inställningar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan, väljer **Användare**och väljer sedan **Alla användare:**

    ![Välj alla användare](common/users.png)

2. Välj **Ny användare** högst upp i fönstret:

    ![Välj Ny användare](common/new-user.png)

3. Gör följande i dialogrutan **Användare.**

    ![Dialogrutan Användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **Användarnamn** anger du **BrittaSimon@\<ditt företag>.\< förlängning>**. (Till exempel BrittaSimon@contoso.com.)

    1. Välj **Visa lösenord**och skriv sedan ned värdet i rutan **Lösenord.**

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge henne åtkomst till ThirdLight.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **ThirdLight**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **ThirdLight**i listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-thirdlight-test-user"></a>Skapa en TredjeLight-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på ThirdLight måste du lägga till dem i ThirdLight. Du måste lägga till dem manuellt.

Så här skapar du ett användarkonto:

1. Logga in på din ThirdLight-företagswebbplats som administratör.

1. Gå till fliken **Användare.**

1. Välj **Användare och grupper**.

1. Välj **Lägg till ny användare**.

1. Ange användarnamn, namn eller beskrivning och e-postadressen till ett giltigt Azure AD-konto som du vill etablera. Välj en förinställning eller grupp med nya medlemmar.

1. Välj **Skapa**.

> [!NOTE]
> Du kan använda alla verktyg för att skapa användarkonton eller API som tillhandahålls av ThirdLight för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer panelen ThirdLight på åtkomstpanelen bör du automatiskt loggas in på den ThirdLight-instans som du ställer in SSO för. Mer information om Åtkomstpanelen finns i [Komma till åtkomst och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
