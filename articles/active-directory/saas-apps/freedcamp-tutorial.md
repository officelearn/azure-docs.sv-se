---
title: 'Självstudier: Azure Active Directory-integrering med Freedcamp | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101919"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Självstudier: Integrera Freedcamp med Azure Active Directory

I de här självstudierna lär du dig att integrera Freedcamp med Azure Active Directory (AD Azure). När du integrerar Freedcamp med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Freedcamp.
* Ge dina användare att automatiskt inloggad till Freedcamp med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Freedcamp enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Freedcamp **SP och IDP** -initierad SSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Att lägga till Freedcamp från galleriet

För att konfigurera integrering av Freedcamp i Azure AD, som du behöver lägga till Freedcamp från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Freedcamp** i sökrutan.
1. Välj **Freedcamp** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Freedcamp med en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Freedcamp för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Freedcamp, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Freedcamp](#configure-freedcamp)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Freedcamp](#create-freedcamp-test-user)**  har en motsvarighet för Britta Simon i Freedcamp som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Freedcamp** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    1. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Användare kan också ange url-värden med avseende på sin egen kund-domän och de kanske inte nödvändigtvis av mönstret `freedcamp.com`, de kan ange ett kund domän specifikt värde, specifik för deras programinstans. Du kan också kontakta [Freedcamp klienten supportteamet](mailto:devops@freedcamp.com) mer information om url-mönster.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera Freedcamp** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurera Freedcamp

1. Om du vill automatisera konfigurationen inom Freedcamp, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Freedcamp** omdirigerar dig till programmet Freedcamp. Ange administratörsautentiseringsuppgifter för att logga in på Freedcamp därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 5.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Freedcamp manuellt, öppna ett nytt webbläsarfönster och logga till Freedcamp företagets webbplatsen som administratör och utför följande steg:

4. I det övre högra hörnet på sidan, klickar du på **profil** och gå sedan till **mitt konto**.

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config01.png)

5. Från vänster sida av menyraden klickar du på **SSO** på den **Your SSO anslutningar** sidan utför följande steg:

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config02.png)

    a. I den **rubrik** text skriver du rubriken.

    b. I den **entitets-ID** textrutan, klistra in den **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    c. I den **inloggnings-URL** textrutan, klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    d. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.

    e. Klicka på **Skicka**.

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Freedcamp.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Freedcamp**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-freedcamp-test-user"></a>Skapa Freedcamp testanvändare

Om du vill aktivera Azure AD-användare registrera i att Freedcamp, de måste etableras i Freedcamp. I Freedcamp är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. I ett annat webbläsarfönster, loggar du in Freedcamp som en administratör.

2. I det översta toright hörnet på sidan, klickar du på **profil** och gå sedan till **hantera System**.

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config03.png)

3. Till höger på sidan Hantera System utför du följande steg:

    ![Freedcamp konfiguration](./media/freedcamp-tutorial/config04.png)

    a. Klicka på **Lägg till eller be användare**.

    b. I den **e-post** text, ange den e-postadressen för användaren som `Brittasimon@contoso.com`.

    c. Klicka på **lägga till användare**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Freedcamp i åtkomstpanelen, bör det vara loggas in automatiskt till Freedcamp som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)