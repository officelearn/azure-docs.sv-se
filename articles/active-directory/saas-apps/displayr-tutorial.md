---
title: 'Självstudier: Azure Active Directory-integrering med Displayr | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7a2d793a1fbd68d6a71f48b556a77ddcaaaf111
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66112154"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Självstudier: Integrera Displayr med Azure Active Directory

I de här självstudierna lär du dig att integrera Displayr med Azure Active Directory (AD Azure). När du integrerar Displayr med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Displayr.
* Ge dina användare att automatiskt inloggad till Displayr med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Displayr enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Displayr **SP** -initierad SSO.

## <a name="adding-displayr-from-the-gallery"></a>Att lägga till Displayr från galleriet

För att konfigurera integrering av Displayr i Azure AD, som du behöver lägga till Displayr från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Displayr** i sökrutan.
1. Välj **Displayr** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Displayr med en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Displayr för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Displayr, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Displayr](#configure-displayr)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Displayr](#create-displayr-test-user)**  har en motsvarighet för Britta Simon i Displayr som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Displayr** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<YOURDOMAIN>.displayr.com`

    b. I den **identifierare (entitets-ID)** text skriver en URL med hjälp av följande mönster:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Displayr klienten supportteamet](mailto:support@displayr.com) att hämta dessa värden. Du kan också referera till de mönster som visas i avsnittet grundläggande SAML-konfiguration i Azure-portalen.

1. På den **Konfigurera enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. Displayr program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Förutom ovanstående Displayr program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I den **användarattribut och anspråk** avsnittet på den **gruppanspråk (förhandsversion)** dialogrutan utför följande steg:

    a. Klicka på den **penna** bredvid **grupper returneras i anspråk**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Välj **alla grupper** från listan över radio.

    c. Välj **källattributet** av **grupp-ID:**.

    d. Kontrollera **har ändrat namnet på gruppanspråket**.

    e. Kontrollera **generera grupper som rollanspråk**.

    f. Klicka på **Spara**.

1. På den **konfiguration Displayr** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Konfigurera Displayr

1. Om du vill automatisera konfigurationen inom Displayr, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Displayr** omdirigerar dig till programmet Displayr. Ange administratörsautentiseringsuppgifter för att logga in på Displayr därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 6.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill ställa in Displayr manuellt, öppna ett nytt webbläsarfönster och logga till Displayr företagets webbplatsen som administratör och utför följande steg:

4. Klicka på **inställningar** gå sedan till **konto**.

    ![Konfiguration](./media/displayr-tutorial/config01.png)

5. Växla till **inställningar** från den översta menyn och rulla nedåt på sidan för att klicka på **Konfigurera enkel inloggning på (SAML)**.

    ![Konfiguration](./media/displayr-tutorial/config02.png)

6. På den **enkel inloggning på (SAML)** utför följande steg:

    ![Konfiguration](./media/displayr-tutorial/config03.png)

    a. Kontrollera den **aktivera enkel inloggning på (SAML)** box.

    b. Kopiera den faktiska **identifierare** värdet från den **SAML grundkonfiguration** avsnitt av Azure AD och klistra in den i den **utfärdare** textrutan.

    c. I den **inloggnings-URL** text rutan, klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    d. I den **URL för utloggning** text rutan, klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    e. Öppna certifikat (Base64) i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.

    f. **Gruppera mappningar** är valfria.

    g. Klicka på **Spara**.  

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Displayr.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Displayr**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-displayr-test-user"></a>Skapa Displayr testanvändare

Om du vill aktivera Azure AD-användare registrera i att Displayr, de måste etableras i Displayr. I Displayr är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Displayr som administratör.

2. Klicka på **inställningar** gå sedan till **konto**.

    ![Displayr konfiguration](./media/displayr-tutorial/config01.png)

3. Växla till **inställningar** från den översta menyn och rulla nedåt på sidan tills **användare** avsnittet och klicka sedan på **ny användare**.

    ![Displayr konfiguration](./media/displayr-tutorial/config07.png)

4. På den **ny användare** utför följande steg:

    ![Displayr konfiguration](./media/displayr-tutorial/config06.png)

    a. I **namn** text, ange namnet på användaren som **Brittasimon**.

    b. I **e-post** text, ange den e-postadressen för användaren som `Brittasimon@contoso.com`.

    c. Välj din lämpliga **gruppmedlemskap**.

    d. Klicka på **Spara**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Displayr i åtkomstpanelen, bör det vara loggas in automatiskt till Displayr som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)