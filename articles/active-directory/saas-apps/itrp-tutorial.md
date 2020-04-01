---
title: 'Självstudiekurs: Azure Active Directory-integrering med ITRP | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67656696"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Självstudiekurs: Azure Active Directory-integrering med ITRP

I den här självstudien får du lära dig hur du integrerar ITRP med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till ITRP.
* Du kan aktivera dina användare så att de automatiskt loggas in på ITRP (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med ITRP måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En ITRP-prenumeration som har enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* ITRP stöder SP-initierad SSO.

## <a name="add-itrp-from-the-gallery"></a>Lägg till ITRP från galleriet

Om du vill konfigurera integreringen av ITRP i Azure AD måste du lägga till ITRP från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program:**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Ange **ITRP**i sökrutan . Välj **ITRP** i sökresultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med ITRP med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i ITRP.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med ITRP måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera ITRP enkel inloggning på](#configure-itrp-single-sign-on)** programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en ITRP-testanvändare](#create-an-itrp-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med ITRP:

1. Välj Enkel inloggning på sidan ITRP-programintegrering i [Azure-portalen](https://portal.azure.com/): **Single sign-on**

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. Gör följande i dialogrutan **Grundläggande SAML-konfiguration.**

    ![Dialogrutan Grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. Ange en WEBBADRESS i det här mönstret i rutan **Logga in på webbadress:**
    
       `https://<tenant-name>.itrp.com`

    1. Ange en URL i det här mönstret i rutan **Identifierare (entitets-ID):**

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda den faktiska inloggnings-URL:en och identifieraren. Kontakta [ITRP:s supportteam](https://www.itrp.com/support) för att få värdena. Du kan också referera till de mönster som visas i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Öppna dialogrutan **Edit** **SAML-signeringscertifikat** i avsnittet **SAML-signeringscertifikat:**

    ![Redigera-ikonen](common/edit-certificate.png)

6. Kopiera **tumavtrycksvärdet** i dialogrutan **SAML-signeringscertifikat** och spara det:

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera ITRP** kopierar du lämpliga webbadresser baserat på dina krav:

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurera ITRP enkel inloggning

1. Logga in på ITRP-företagets webbplats som administratör i ett nytt webbläsarfönster.

1. Högst upp i fönstret väljer du ikonen **Inställningar:**

    ![Ikonen Inställningar](./media/itrp-tutorial/ic775570.png "Ikonen Inställningar")

1. Välj Enkel inloggning **i**den vänstra rutan:

    ![Välj enkel inloggning](./media/itrp-tutorial/ic775571.png "Välj enkel inloggning")

1. I avsnittet **Konfiguration för enkel inloggning** gör du följande.

    ![Avsnittet Enkel inloggning](./media/itrp-tutorial/ic775572.png "Avsnittet Enkel inloggning")

    ![Avsnittet Enkel inloggning](./media/itrp-tutorial/ic775573.png "Avsnittet Enkel inloggning")

    1. Välj **Aktiverad**.

    1. I rutan **URL för fjärrutloggning** klistrar du in **url-värdet för utloggning** som du kopierade från Azure-portalen.

    1. I rutan **SAML SSO-URL** klistrar du in värdet **för inloggnings-URL** som du kopierade från Azure-portalen.

    1. I rutan **Certifikatfingeravtryck** klistrar du in **tumavtrycksvärdet** för certifikatet, som du kopierade från Azure-portalen.

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

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge henne åtkomst till ITRP.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **ITRP**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **ITRP**i listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-itrp-test-user"></a>Skapa en ITRP-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på ITRP måste du lägga till dem i ITRP. Du måste lägga till dem manuellt.

Så här skapar du ett användarkonto:

1. Logga in på din ITRP-klient.

1. Högst upp i fönstret väljer du ikonen **Poster:**

    ![Ikon för poster](./media/itrp-tutorial/ic775575.png "Ikon för poster")

1. Välj **Personer**på menyn:

    ![Välj Personer](./media/itrp-tutorial/ic775587.png "Välj Personer")

1. Välj plustecknet**+**( ) om du vill lägga till en ny person:

    ![Välj plustecknet](./media/itrp-tutorial/ic775576.png "Välj plustecknet")

1. Gör följande i dialogrutan **Lägg till ny person.**

    ![Dialogrutan Lägg till ny person](./media/itrp-tutorial/ic775577.png "Dialogrutan Lägg till ny person")

    1. Ange namn och e-postadress för ett giltigt Azure AD-konto som du vill lägga till.

    1. Välj **Spara**.

> [!NOTE]
> Du kan använda alla verktyg för att skapa användarkonton eller API som tillhandahålls av ITRP för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer ITRP-panelen på åtkomstpanelen ska du automatiskt loggas in i den ITRP-instans som du ställer in SSO för. Mer information om Åtkomstpanelen finns i [Komma till åtkomst och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
