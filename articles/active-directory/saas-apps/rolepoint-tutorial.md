---
title: 'Självstudiekurs: Azure Active Directory-integrering med RolePoint | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RolePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fd17d2f8577532778733866260f43e9ac7685
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092736"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Självstudiekurs: Azure Active Directory-integrering med RolePoint

I den här självstudien får du lära dig hur du integrerar RolePoint med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till RolePoint.
* Du kan aktivera dina användare så att de automatiskt loggas in på RolePoint (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med RolePoint måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En RolePoint-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* RolePoint stöder SP-initierad SSO.

## <a name="add-rolepoint-from-the-gallery"></a>Lägga till RolePoint från galleriet

Om du vill konfigurera integreringen av RolePoint i Azure AD måste du lägga till RolePoint från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program:**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Ange **RolePoint**i sökrutan . Välj **RolePoint** i sökresultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med RolePoint med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i RolePoint.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med RolePoint måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera Enstaka signering](#configure-rolepoint-single-sign-on)** av RolePoint på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en RolePoint-testanvändare](#create-a-rolepoint-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med RolePoint:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **RolePoint-programintegrering:**

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. Gör följande i dialogrutan **Grundläggande SAML-konfiguration.**

    ![Dialogrutan Grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. Ange en WEBBADRESS i det här mönstret i rutan **Logga in på webbadress:**

       `https://<subdomain>.rolepoint.com/login`

    1. Ange en URL i det här mönstret i rutan **Identifierare (entitets-ID):**

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda den faktiska inloggnings-URL:en och identifieraren. Vi föreslår att du använder ett unikt strängvärde i identifieraren. Kontakta [Supportteamet](mailto:info@rolepoint.com) för RolePoint för att få dessa värden. Du kan också referera till de mönster som visas i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du länken **Hämta bredvid** **FEDERATIONMetadata-XML**enligt dina krav och sparar filen på datorn.

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera Rollpoint** kopierar du lämpliga webbadresser baserat på dina krav:

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.


### <a name="configure-rolepoint-single-sign-on"></a>Konfigurera enkel inloggning i RolePoint

Om du vill konfigurera enkel inloggning på RolePoint-sidan måste du arbeta med Supportteamet för [RolePoint](mailto:info@rolepoint.com). Skicka det här teamet XML-filen federationsmetadata och url:erna som du fick från Azure-portalen. De konfigurerar RolePoint för att säkerställa att SAML SSO-anslutningen är korrekt inställd på båda sidor.

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

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge henne åtkomst till RolePoint.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **RolePoint**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **RolePoint**i listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-rolepoint-test-user"></a>Skapa en RollPoint-testanvändare

Därefter måste du skapa en användare med namnet Britta Simon i RolePoint. Arbeta med Supportteamet för [RolePoint](mailto:info@rolepoint.com) för att lägga till användare i RolePoint. Användare måste skapas och aktiveras innan du kan använda enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer rollpunktspanelen på åtkomstpanelen bör du automatiskt loggas in i den RolePoint-instans som du konfigurerar SSO för. Mer information om Åtkomstpanelen finns i [Komma till åtkomst och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
