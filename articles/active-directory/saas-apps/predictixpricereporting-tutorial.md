---
title: 'Självstudiekurs: Azure Active Directory-integrering med Predictix prisrapportering | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Predictix Price Reporting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 808b2d964bb39af6b410a84563717102ebece454
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094112"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Självstudiekurs: Azure Active Directory-integrering med Predictix-prisrapportering

I den här självstudien får du lära dig hur du integrerar Predictix prisrapportering med Azure Active Directory (Azure AD).

Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till Predictix prisrapportering.
* Du kan aktivera dina användare så att de automatiskt loggas in på Predictix Prisrapportering (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Predictix-prisrapportering behöver du:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [månads utvärderingsprenumeration.](https://azure.microsoft.com/pricing/free-trial/)
* En Predictix Price Reporting-prenumeration som har enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* Predictix Price Reporting stöder SP-initierad SSO.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Lägga till Predictix-prisrapportering från galleriet

Om du vill konfigurera integreringen av Predictix-prisrapportering i Azure AD måste du lägga till Predictix-prisrapportering från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program:**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Skriv **Predictix prisrapportering**i sökrutan . Välj **Predictix prisrapportering** i sökresultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Predictix Prisrapportering med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Predictix Prisrapportering.

Om du vill konfigurera och testa azure AD-enkel inloggning med Predictix prisrapportering måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. Konfigurera Enkel inloggning för **[Predictix-prisrapportering på](#configure-predictix-price-reporting-single-sign-on)** programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en Predictix Prisrapporteringstestanvändare](#create-a-predictix-price-reporting-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Predictix-prisrapportering:

1. På [Azure portal](https://portal.azure.com/)sidan Integrering av Programmet Försredovisning för Predictix Prisrapportering väljer du **Enkel inloggning på**sidan För **programintegrering av Förutsägaix prisrapportering:**

    ![Välj Enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. Gör följande i dialogrutan **Grundläggande SAML-konfiguration.**

    ![Dialogrutan Grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. Ange en WEBBADRESS i det här mönstret i rutan **Logga in på webbadress:**

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Ange en URL i det här mönstret i rutan **Identifierare (entitets-ID):**

        | |
        |--|
        | `https://<companyname-pricing>.predictix.com` |
        | `https://<companyname-pricing>.dev.predictix.com` |
        | |

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda den faktiska inloggnings-URL:en och identifieraren. Kontakta [Supportteamet för Predictix Prisrapportering](https://www.infor.com/company/customer-center/) för att få värdena. Du kan också referera till de mönster som visas i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du länken **Hämta bredvid** **Certifikat (Base64)** enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Predictix-prisrapportering** kopierar du lämpliga webbadresser baserat på dina krav.

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Konfigurera enkel inloggning för Predictix-prisrapportering

Om du vill konfigurera enkel inloggning på Predictix-prisrapporteringssidan måste du skicka certifikatet som du hämtade och webbadresserna som du kopierade från Azure-portalen till [Supportteamet för Predictix-prisrapportering](https://www.infor.com/company/customer-center/). Det här teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan, väljer **Användare**och väljer sedan **Alla användare:**

    ![Välj alla användare](common/users.png)

2. Välj **Ny användare** högst upp på skärmen:

    ![Välj Ny användare](common/new-user.png)

3. Gör följande i dialogrutan **Användare.**

    ![Dialogrutan Användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **Användarnamn** anger du **BrittaSimon@\<ditt företag>.\< förlängning>**. (Till exempel BrittaSimon@contoso.com.)

    1. Välj **Visa lösenord**och skriv sedan ned värdet i rutan **Lösenord.**

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda azure AD enkel inloggning genom att ge henne åtkomst till Predictix prisrapportering.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **Predictix Prisrapportering**.

    ![Företagsprogram](common/enterprise-applications.png)

2. Välj **Predictix prisrapportering**i listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Skapa en Predictix Prisrapporteringstestanvändare

Därefter måste du skapa en användare som heter Britta Simon i Predictix Prisrapportering. Arbeta med [Supportteamet för Predictix Prisrapportering](https://www.infor.com/company/customer-center/) för att lägga till användare. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer panelen Predictix prisrapportering på åtkomstpanelen bör du automatiskt loggas in i den Predictix-prisrapporteringsinstans som du ställer in SSO för. Mer information finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)