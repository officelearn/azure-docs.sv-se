---
title: 'Självstudier: Azure Active Directory-integrering med Freshservice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fbf69ba814b99434e933ed700fb0c8c842c3312
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101843"
---
# <a name="tutorial-integrate-freshservice-with-azure-active-directory"></a>Självstudier: Integrera Freshservice med Azure Active Directory

I de här självstudierna lär du dig att integrera Freshservice med Azure Active Directory (AD Azure). När du integrerar Freshservice med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Freshservice.
* Ge dina användare att automatiskt inloggad till Freshservice med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration Freshservice enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Freshservice stöder **IDP**-initierad enkel inloggning

## <a name="adding-freshservice-from-the-gallery"></a>Lägga till Freshservice från galleriet

För att konfigurera integreringen av Freshservice till Azure AD behöver du lägga till Freshservice från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Freshservice** i sökrutan.
1. Välj **Freshservice** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Freshservice med en testanvändare kallas **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Freshservice.

Om du vill konfigurera och testa Azure AD enkel inloggning med Freshservice, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Freshservice SSO](#configure-freshservice-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Freshservice-testanvändare](#create-freshservice-test-user)** – för att ha en motsvarighet för Britta Simon i Freshservice som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Freshservice** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<democompany>.freshservice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Hämta dessa värden genom att kontakta [supportteamet för Freshservice-klienten](https://support.freshservice.com/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** kopierar du **Tumavtryck** och sparar det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera Freshservice** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-freshservice-sso"></a>Konfigurera Freshservice SSO

1. Om du vill automatisera konfigurationen inom Freshservice, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Freshservice** omdirigerar dig till programmet Freshservice. Ange administratörsautentiseringsuppgifter för att logga in på Freshservice därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 6.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Freshservice manuellt, öppna ett nytt webbläsarfönster och logga till Freshservice företagets webbplatsen som administratör och utför följande steg:

4. På menyn längst upp klickar du på **Admin**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

5. I **kundportalen** klickar du på **Säkerhet**.

    ![Säkerhet](./media/freshservice-tutorial/ic790815.png "Säkerhet")

6. I avsnittet **Säkerhet** utför du följande steg:

    ![Enkel inloggning](./media/freshservice-tutorial/ic790816.png "Enkel inloggning")

    a. Växla **Enkel inloggning**.

    b. Välj **SAML SSO**.

    c. I textrutan för **inloggnings-URL för SAML** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    e. I textrutan **Security Certificate Fingerprint** (Fingeravtryck för säkerhetscertifikat) klistrar du in värdet för det **THUMBPRINT** (Tumavtryck) för certifikatet som du har kopierat från Azure-portalen.

    f. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas Britta Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Freshservice.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I programlistan väljer du **Freshservice**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-freshservice-test-user"></a>Skapa Freshservice-testanvändare

Om du vill aktivera Azure AD-användare att logga in på FreshService, måste de etableras i FreshService. När det gäller FreshService är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **FreshService** företagets plats som administratör.

2. På menyn längst upp klickar du på **Admin**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

3. I avsnittet **Användarhantering** klickar du på **Beställare**.

    ![Beställare](./media/freshservice-tutorial/ic790818.png "Beställare")

4. Klicka på **Ny beställare**.

    ![Nya beställare](./media/freshservice-tutorial/ic790819.png "Nya beställare")

5. I avsnittet **Ny beställare** utför du följande steg:

    ![Ny beställare](./media/freshservice-tutorial/ic790820.png "Ny beställare")  

    a. Ange attributen **Förnamn** och **E-post** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

    b. Klicka på **Spara**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt
    >  

> [!NOTE]
> Du kan använda andra verktyg eller API:er för FreshService-kontoskapande som tillhandahålls av FreshService för att etablera AAD-användarkonton.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Freshservice i åtkomstpanelen, bör det vara loggas in automatiskt till Freshservice som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
