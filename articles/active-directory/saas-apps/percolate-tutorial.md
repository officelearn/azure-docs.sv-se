---
title: 'Självstudiekurs: Azure Active Directory-integrering med Percolate | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094603"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Självstudiekurs: Azure Active Directory-integrering med Percolate

I den här självstudien får du lära dig hur du integrerar Percolate med Azure Active Directory (Azure AD).

Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till Percolate.
* Du kan aktivera dina användare så att de automatiskt loggas in på Percolate (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Percolate måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En Percolate-prenumeration som har enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* Percolate stöder SP-initierad och IdP-initierad SSO.

## <a name="add-percolate-from-the-gallery"></a>Lägga till Percolate från galleriet

Om du vill konfigurera integreringen av Percolate i Azure AD måste du lägga till Percolate från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program:**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Skriv **Percolate**i sökrutan . Välj **Percolate** i sökresultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Percolate med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Percolate.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Percolate måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera Percolate enkel inloggning på](#configure-percolate-single-sign-on)** programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en percolate-testanvändare](#create-a-percolate-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Percolate:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Percolate-programintegration:** **Single sign-on**

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. I dialogrutan **Grundläggande SAML-konfiguration** behöver du inte vidta några åtgärder för att konfigurera programmet i IdP-initierat läge. Appen är redan integrerad med Azure.

    ![Förtala domän och webbadresser med enkel inloggning](common/preintegrated.png)

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare webbadresser** och anger i rutan Logga in **https://percolate.com/app/login** **på URL:**

   ![Förtala domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)
6. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen Kopiera i avsnittet **Saml-signeringscertifikat** för att kopiera **url:en url till appfederationens metadata**. **Copy** Spara den här webbadressen.

    ![Kopiera URL:en för metadata för App Federation](common/copy-metadataurl.png)

7. Kopiera lämpliga webbadresser i avsnittet **Konfigurera perkolat,** baserat på dina krav.

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurera Percolate enkel inloggning

1. I ett nytt webbläsarfönster loggar du in på Percolate som administratör.

2. På startsidans vänstra sida väljer du **Inställningar:**
    
    ![Välj Inställningar](./media/percolate-tutorial/configure01.png)

3. Välj **SSO** i den vänstra rutan under **Organisation:**

    ![Välj SSO under Organisation](./media/percolate-tutorial/configure02.png)

    1. I rutan **Inloggnings-URL** klistrar du in värdet **för inloggningsadressen** som du kopierade från Azure-portalen.

    1. I rutan **Entitets-ID** klistrar du in **azure AD-identifierare** som du kopierade från Azure-portalen.

    1. Öppna det base-64-kodade certifikatet som du hämtade från Azure-portalen i Anteckningar. Kopiera innehållet och klistra in det i rutan **x509-certifikat.**

    1. Ange **e-postadress**i rutan **E-postattribut** .

    1. **URL-rutan Identitetsproviderns metadata** är ett valfritt fält. Om du kopierade en **url för metadata för appfederation** från Azure-portalen kan du klistra in den i den här rutan.

    1. **I**listan **Ska AuthNRequests signeras?**

    1. I listan **Aktivera SSO-automatisk etablering** väljer du **Nej**.

    1. Välj **Spara**.

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

I det här avsnittet ska du aktivera Britta Simon för att använda azure AD enkel inloggning genom att ge henne åtkomst till Percolate.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **Percolate**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Percolate i**listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Användare och grupper](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-percolate-test-user"></a>Skapa en percolate-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Percolate måste du lägga till dem i Percolate. Du måste lägga till dem manuellt.

Så här skapar du ett användarkonto:

1. Logga in på Percolate som administratör.

2. Välj **Användare** under **Organisation**i den vänstra rutan . Välj **Nya användare:**

    ![Välj Nya användare](./media/percolate-tutorial/configure03.png)

3. Gör följande på sidan **Skapa användare.**

    ![Sidan Skapa användare](./media/percolate-tutorial/configure04.png)

    1. Ange användarens e-postadress i rutan **E-post.** Till exempel brittasimon@contoso.com.

    1. Ange användarens namn i rutan **Fullständigt namn.** Till exempel **Brittasimon**.

    1. Välj **Skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer panelen Percolate på åtkomstpanelen bör du automatiskt loggas in på den Percolate-instans som du ställer in SSO för. Mer information finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)