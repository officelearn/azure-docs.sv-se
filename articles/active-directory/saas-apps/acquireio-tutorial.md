---
title: 'Självstudier: Azure Active Directory-integrering med AcquireIO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75a92e78e7293316cad6e567ae49c4998299415c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544272"
---
# <a name="tutorial-integrate-acquireio-with-azure-active-directory"></a>Självstudier: Integrera AcquireIO med Azure Active Directory

I de här självstudierna lär du dig att integrera AcquireIO med Azure Active Directory (AD Azure). När du integrerar AcquireIO med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till AcquireIO.
* Ge dina användare att automatiskt inloggad till AcquireIO med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration AcquireIO enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för AcquireIO **IDP** -initierad SSO.

## <a name="adding-acquireio-from-the-gallery"></a>Att lägga till AcquireIO från galleriet

För att konfigurera integrering av AcquireIO i Azure AD, som du behöver lägga till AcquireIO från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **AcquireIO** i sökrutan.
1. Välj **AcquireIO** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med AcquireIO med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i AcquireIO för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med AcquireIO, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera AcquireIO](#configure-acquireio)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare AcquireIO](#create-acquireio-test-user)**  har en motsvarighet för B.Simon i AcquireIO som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **AcquireIO** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Värdet är inte verkligt. Du får de faktiska svars-URL som beskrivs senare i den **konfigurera AcquireIO** avsnittet av självstudiekursen. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera AcquireIO** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-acquireio"></a>Konfigurera AcquireIO

1. I ett annat webbläsarfönster, loggar du in AcquireIO som administratör.

2. Från vänster sida av menyn, klickar du på **App Store**.

     ![AcquireIO konfiguration](./media/acquireio-tutorial/config01.png)

3. Rulla ned till och **Active Directory** och klicka på **installera**.

    ![AcquireIO konfiguration](./media/acquireio-tutorial/config02.png)

4. Utför följande steg på popup-fönster med Active Directory:

    ![AcquireIO konfiguration](./media/acquireio-tutorial/config03.png)

    a. Klicka på **kopia** kopiera svars-URL för din instans och klistra in den i **svars-URL** -textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

    b. I den **inloggnings-URL** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet och klistra in den i den **X.509-certifikat** textrutan.

    d. Klicka på **ansluta nu**.

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

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AcquireIO.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **AcquireIO**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-acquireio-test-user"></a>Skapa AcquireIO testanvändare

Om du vill aktivera Azure AD-användare att logga in på AcquireIO, måste de etableras i AcquireIO. I AcquireIO är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. I ett annat webbläsarfönster, loggar du in AcquireIO som administratör.

2. Till vänster i menyn klickar du på **profiler** och gå till **lägga till profilen**.

     ![AcquireIO konfiguration](./media/acquireio-tutorial/config04.png)

3. På den **Lägg till kunden** popup, utför följande steg:

    ![AcquireIO konfiguration](./media/acquireio-tutorial/config05.png)

    a. I **namn** text, ange namnet på användaren som **B.simon**.

    b. I textrutan **E-post** anger du användarens e-postadress som **B.simon@contoso.com** .

    c. Klicka på **Skicka**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen AcquireIO i åtkomstpanelen, bör det vara loggas in automatiskt till AcquireIO som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)