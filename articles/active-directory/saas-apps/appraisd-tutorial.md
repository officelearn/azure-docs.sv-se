---
title: 'Självstudier: Azure Active Directory-integrering med Appraisd | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561206"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Självstudier: Integrera Appraisd med Azure Active Directory

I de här självstudierna lär du dig att integrera Appraisd med Azure Active Directory (AD Azure). När du integrerar Appraisd med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Appraisd.
* Ge dina användare att automatiskt inloggad till Appraisd med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Appraisd enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Appraisd **SP och IDP** -initierad SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Lägga till Appraisd från galleriet

För att konfigurera integrering av Appraisd i Azure AD måste du lägga till Appraisd från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Appraisd** i sökrutan.
1. Välj **Appraisd** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Appraisd med en testanvändare kallas **B. Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Appraisd för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Appraisd, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Appraisd](#configure-appraisd)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B. Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Appraisd](#create-appraisd-test-user)**  har en motsvarighet för B. Simon i Appraisd som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Appraisd** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** avsnittet programmet är förkonfigurerad och nödvändiga URL: er är redan ifyllda på förhand med Azure. Användaren behöver för att spara konfigurationen genom att klicka på knappen Spara och utför följande steg:

    a. Klicka på **Ange ytterligare URL:er**.

    b. Skriv en URL i textrutan **Vidarebefordransstatus**: `<TENANTCODE>`

    c. Om du vill konfigurera programmet i **SP**-initierat läge skriver du en URL med hjälp av följande mönster i textrutan **Inloggnings-URL**: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Du får det faktiska värdet för inloggnings-URL och värdet för vidarebefordransstatus på konfigurationssidan för Appraisd SSO som beskrivs senare i självstudien.

1. Appraisd program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Appraisd program som förväntar **nameidentifier** mappas med **user.mail**, så måste du redigera attribut mappar genom att klicka på **redigera** ikon och ändra den attributmappning.

    ![image](common/edit-attribute.png)

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera Appraisd** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurera Appraisd

1. Om du vill automatisera konfigurationen inom Appraisd, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Appraisd** omdirigerar dig till programmet Appraisd. Ange administratörsautentiseringsuppgifter för att logga in på Appraisd därifrån. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Appraisd manuellt, öppna ett nytt webbläsarfönster och logga till Appraisd företagets webbplatsen som administratör och utför följande steg:

4. Längst upp till höger på sidan, klickar du på **inställningar** ikonen och gå sedan till **Configuration**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. På vänster sida av menyn klickar du på **Enkel inloggning med SAML**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. På sidan för **konfiguration av enkel inloggning för SAML 2.0** utför du följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopiera den **standard Vidarebefordransstatus** och klistra in den i **Vidarebefordransstatus** -textrutan i **grundläggande SAML-konfiguration** på Azure-portalen.

    b. Kopiera den **tjänstinitierade inloggnings-URL** och klistra in den i **inloggnings-URL** -textrutan i **SAML grundkonfiguration** på Azure-portalen.

7. Rulla nedåt på samma sida under **Identifiera användare** och utför följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. I textrutan **Identity Provider Single Sign-On URL** (URL för enkel inloggning för identitetsprovider) klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och klickar på **Spara**.

    b. I den **utfärdar-URL för identitetsprovider** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen och klicka på **spara**.

    c. I anteckningar, öppna Base64-kodade certifikatet som du laddade ned från Azure-portalen, kopiera innehållet och klistra in den i den **X.509-certifikat** och klickar på **spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B. Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B. Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Appraisd.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Appraisd**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B. Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-appraisd-test-user"></a>Skapa Appraisd-testanvändare

För att Azure AD-användare ska kunna logga in på Appraisd måste de etableras till Appraisd. I Appraisd är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Appraisd som administratör.

2. Längst upp till höger på sidan, klickar du på **inställningar** ikonen och gå sedan till **Administration centre**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. I verktygsfältet högst upp på sidan, klickar du på **personer**, gå sedan till **lägga till en ny användare**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Utför följande steg i rutan **Lägg till en ny användare**:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. I textrutan **Förnamn** fyller du i användarens förnamn, till exempel **Britta**.

    b. I textrutan **Efternamn** anger du efternamnet på användaren, till exempel **simon**.

    c. I **e-post** text, ange den e-postadressen för användaren som `B. Simon@contoso.com`.

    d. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Appraisd i åtkomstpanelen, bör det vara loggas in automatiskt till Appraisd som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
