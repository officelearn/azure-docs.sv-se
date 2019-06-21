---
title: 'Självstudier: Azure Active Directory-integrering med sätt vi göra | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hur vi gör.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310406"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Självstudier: Integrera sätt att göra med Azure Active Directory

I de här självstudierna lär du dig att integrera sätt vi göra med Azure Active Directory (AD Azure). När du integrerar sätt vi göra med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till sätt som vi gör.
* Ge dina användare att automatiskt inloggad sätt vi göra med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Sätt som vi med enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Hur vi gör stöder **SP** -initierad SSO
* Hur vi gör stöder **Just In Time** etableringen av användare

## <a name="adding-way-we-do-from-the-gallery"></a>Att lägga till sätt vi göra från galleriet

För att konfigurera integrering av sätt vi göra i Azure AD, som du behöver lägga till sätt vi göra från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **sätt vi göra** i sökrutan.
1. Välj **sätt vi göra** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med sätt vi göra med hjälp av en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den aktuella användaren på sätt som vi gör.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med sätt vi göra:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera hur vi gör SSO](#configure-way-we-do-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa sätt som vi testanvändare](#create-way-we-do-test-user)**  – du har en motsvarighet för Britta Simon på sätt som vi gör som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **sätt vi göra** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [sätt vi gör klienten supportteamet](mailto:support@waywedo.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Raw)** och välj **hämta**att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificateraw.png)

1. På den **ställa in hur vi gör** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurera hur vi gör SSO

1. Om du vill automatisera konfigurationen inom sätt vi göra måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

1. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet sätt vi göra** omdirigerar dig till programmet sätt som vi gör. Ange administratörsautentiseringsuppgifter för att logga in på sätt som vi gör därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 6.

    ![Installationskonfiguration](common/setup-sso.png)

1. Om du vill ställa in hur vi gör manuellt, öppna ett nytt webbläsarfönster och logga in på webbplatsen sätt vi göra om företaget som administratör och utför följande steg:

1. Klicka på den **person ikonen** i det övre högra hörnet på en sida i sätt som vi gör, och klicka på **konto** i den nedrullningsbara menyn.

    ![Hur vi gör konto](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klicka på den **menyikonen** att öppna den push navigeringsmenyn och klicka på **Single Sign On**.

    ![Sätt som vi gör enda](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. På den **installationsprogrammet för enkel inloggning** utför följande steg:

    ![Sätt som vi gör spara](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klicka på den **aktivera enkel inloggning** växla till **Ja** att aktivera enkel inloggning.

    b. I den **namn för enkel inloggning** textrutan, ange ditt namn.

    c. I den **entitets-ID** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    d. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    e. Ladda upp certifikatet genom att klicka på den **Välj** bredvid knappen **certifikat**.

    f. **Valfria inställningar** -
    
    * Aktivera lösenord – när det här alternativet är inaktiverat, regelbundna lösenordet fungerar för hur vi gör så att användarna bara kan använda enkel inloggning.

    * Aktivera automatisk etablering – när den är aktiverad, e-postadressen används för att inloggnings-automatiskt ska jämföras med en lista över användare i sätt som vi gör. Om e-postadressen inte matchar en aktiv användare på sätt som vi gör det lägger automatiskt till ett nytt användarkonto för den person som loggar in, begär eventuell information som saknas.

      > [!NOTE]
      > Användare som har lagts till via enkel inloggning har lagts till som allmänt användare och tilldelats inte en roll i systemet. En administratör kan gå in och ändra deras säkerhetsroll som en redigerare eller administratör och kan också tilldela en eller flera Organisationsschema roller.

    g. Klicka på **spara** att spara dina inställningar.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till sätt som vi gör.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **sätt vi göra**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-way-we-do-test-user"></a>Skapa sätt vi göra testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i sätt som vi gör. Hur vi gör stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns på sätt som vi gör, skapas en ny efter autentisering.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [sätt vi gör klienten supportteamet](mailto:support@waywedo.com).

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen sätt som vi gör i åtkomstpanelen, bör det vara loggas in automatiskt till de sätt vi gör som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)