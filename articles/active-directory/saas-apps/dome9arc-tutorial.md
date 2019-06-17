---
title: 'Självstudier: Azure Active Directory-integrering med Kontrollera punkt CloudGuard Dome9 båge | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och kontrollera punkt CloudGuard Dome9 båge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdaaab8257d3a79130902e1ba0466f9cf15484f4
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147136"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Självstudier: Integrera Check Point CloudGuard Dome9 båge med Azure Active Directory

I de här självstudierna lär du dig att integrera Kontrollera punkt CloudGuard Dome9 båge med Azure Active Directory (AD Azure). När du integrerar Kontrollera punkt CloudGuard Dome9 båge med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Kontrollera punkt CloudGuard Dome9 båge.
* Ge dina användare att automatiskt inloggad till Kontrollera punkt CloudGuard Dome9 båge med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Kontrollera punkt CloudGuard Dome9 båge enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Kontrollera punkt CloudGuard Dome9 båge stöder **SP och IDP** -initierad SSO.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Att lägga till Kontrollera punkt CloudGuard Dome9 båge från galleriet

Om du vill konfigurera integreringen av Kontrollera punkt CloudGuard Dome9 bågen till Azure AD, som du behöver lägga till Kontrollera punkt CloudGuard Dome9 båge från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Kontrollera punkt CloudGuard Dome9 båge** i sökrutan.
1. Välj **Kontrollera punkt CloudGuard Dome9 båge** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Kontrollera punkt CloudGuard Dome9 båge med en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Kontrollera punkt CloudGuard Dome9 båge.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kontrollera punkt CloudGuard Dome9 båge, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Kontrollera punkt CloudGuard Dome9 båge](#configure-check-point-cloudguard-dome9-arc)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa Kontrollera punkt CloudGuard Dome9 båge testanvändare](#create-check-point-cloudguard-dome9-arc-test-user)**  har en motsvarighet för B.Simon i Kontrollera punkt CloudGuard Dome9 båge som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Kontrollera punkt CloudGuard Dome9 båge** programsidan integration, hitta den **hantera** och väljer **enkel inloggning** .
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://secure.dome9.com/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Du väljer värdet för ditt företagsnamn i dome9-administratörsportalen, vilket förklaras senare i den här självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [Kontrollera punkt CloudGuard Dome9 båge klienten supportteamet](mailto:Dome9@checkpoint.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Check Point CloudGuard Dome9 båge program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

7. Dessutom ovan kan kontrollera punkt CloudGuard Dome9 båge program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn |  Källattribut|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera Kontrollera punkt CloudGuard Dome9 båge** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Konfigurera Check Point CloudGuard Dome9 båge

1. Logga in på webbplatsen Kontrollera punkt CloudGuard Dome9 båge företag som en administratör i ett annat webbläsarfönster.

2. Klicka på **profilinställningarna** uppe i högra hörnet och klicka sedan på **Account Settings** (Kontoinställningar). 

    ![Kontrollera konfigurationen för punkt CloudGuard Dome9 båge](./media/dome9arc-tutorial/configure1.png)

3. Gå till **SSO** och klicka sedan på **ENABLE**.

    ![Kontrollera konfigurationen för punkt CloudGuard Dome9 båge](./media/dome9arc-tutorial/configure2.png)

4. Utför följande steg i konfigurationsavsnittet för enkel inloggning:

    ![Kontrollera konfigurationen för punkt CloudGuard Dome9 båge](./media/dome9arc-tutorial/configure3.png)

    a. Ange företagets namn i textrutan **Account ID** (Konto-ID). Det här värdet som ska användas i svars-URL som nämns i Azure portal **SAML grundkonfiguration** avsnittet.

    b. I den **utfärdare** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure portal.

    c. I textrutan **Idp endpoint url** (url till Idp-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).

    e. Klicka på **Spara**.

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

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning om du beviljar åtkomst till Kontrollera punkt CloudGuard Dome9 båge.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Kontrollera punkt CloudGuard Dome9 båge**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Skapa Kontrollera punkt CloudGuard Dome9 båge testanvändare

Om du vill aktivera Azure AD-användare att logga in på Kontrollera punkt CloudGuard Dome9 båge, måste de etableras i program. Kontrollera punkt CloudGuard Dome9 båge stöder just-in-time-etablering men för att det ska fungera korrekt, användaren måste välja specifika **rollen** och tilldela den till användaren.

   >[!Note]
   >För **rollen** skapande och annan information Kontakta [Kontrollera punkt CloudGuard Dome9 båge klienten supportteamet](mailto:Dome9@checkpoint.com).

**Utför följande steg om du vill etablera ett användarkonto manuellt:**

1. Logga in på webbplatsen Kontrollera punkt CloudGuard Dome9 båge företagets som administratör.

2. Klicka på **Users & Roles** (Användare och roller) och sedan på **Users** (Användare).

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user1.png)

3. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user2.png)

4. I avsnittet **Skapa användare** utför du följande steg:

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user3.png)

    a. I textrutan **E-post** skriver du e-postadressen för användaren, till exempel B.Simon@contoso.com.

    b. I den **Förnamn** textrutan Ange först namnet på användaren som B.

    c. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    d. Välj **På** för **användaren**.

    e. Klicka på **SKAPA**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Kontrollera punkt CloudGuard Dome9 båge i åtkomstpanelen, bör det vara loggas in automatiskt att kontrollera punkt CloudGuard Dome9 bågen som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)