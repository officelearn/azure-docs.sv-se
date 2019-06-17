---
title: 'Självstudier: Azure Active Directory-integrering med Atlassian Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbfb07b73d591bbab64f38e8c986fb1b7e072a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106594"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Självstudier: Integrera Atlassians molnet med Azure Active Directory

I de här självstudierna lär du dig att integrera Atlassians molnet med Azure Active Directory (AD Azure). När du integrerar Atlassians molnet med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Atlassians moln.
* Ge dina användare att automatiskt inloggad Atlassians molnet med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration Atlassians molnet enkel inloggning (SSO).
* Om du vill aktivera enkel inloggning med SAML för Atlassian Cloud-produkter måste du konfigurera Atlassian-åtkomst. Läs mer om [Atlassians-åtkomst]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Atlassian Cloud stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Lägga till Atlassian Cloud från galleriet

Om du vill konfigurera integreringen av Atlassian Cloud i Azure AD måste du lägga till Atlassian Cloud från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Atlassians molnet** i sökrutan.
1. Välj **Atlassians molnet** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Atlassians molnet med en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Atlassians molnet.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Atlassians molnet:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Atlassians molnet SSO](#configure-atlassian-cloud-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa Atlassians molnet testanvändare](#create-atlassian-cloud-test-user)**  – du har en motsvarighet för B.Simon i Atlassians moln som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Atlassians molnet** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, anger du värden för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://auth.atlassian.com/saml/<unique ID>`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Föregående värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Du får dessa verkliga värden från den **Atlassians Cloud SAML-konfiguration** skärmen som beskrivs senare i den **konfigurera Atlassians molnet enkel inloggning** av kursen.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Klistra in värdet från den instans som du använder för att logga in på administrationsportalen för Atlassians molnet för.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Ditt Atlassian Cloud-program förväntar sig SAML-intygen i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Atlassian Cloud-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera Atlassian Cloud** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-atlassian-cloud-sso"></a>Konfigurera Atlassians moln SSO

1. Om du vill automatisera konfigurationen inom Atlassians moln, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Atlassians molnet** omdirigerar dig till Atlassians molnprogram. Ange administratörsautentiseringsuppgifter för att logga in på molnet Atlassians därifrån. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Atlassians molnet manuellt, öppna ett nytt webbläsarfönster och logga till Atlassians molnet företagets webbplatsen som administratör och utför följande steg:

4. Du måste verifiera din domän innan du fortsätter med att konfigurera enkel inloggning. Mer information finns i dokumentet [Atlassian-domänverifiering](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

5. I den vänstra rutan väljer **Security** > **SAML enkel inloggning**. Prenumerera på Atlassian Identity Manager om du inte redan gör det.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

6. Gör följande i fönstret **Lägg till SAML-konfiguration**:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. I den **identitetsprovider entitets-ID** rutan, klistra in den **Azure AD-identifierare** som du kopierade från Azure-portalen.

    b. I den **identitetsprovider SSO URL** rutan, klistra in den **inloggnings-URL** som du kopierade från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure Portal i en txt-fil, kopiera värdet (utan raderna *Starta certifikat* och *Avsluta certifikat*) och klistra in det i rutan **Offentligt X509-certifikat**.

    d. Klicka på **Spara konfiguration**.

7. Säkerställ att du har konfigurerat rätt URL:er genom att uppdatera Azure AD-inställningarna på följande sätt:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. I fönstret SAML kopiera den **SP identitets-ID** och sedan i Azure-portalen under Atlassians molnet **grundkonfiguration SAML**, klistra in den i den **identifierare** box.

    b. I fönstret SAML kopiera den **SP URL för Konsumenttjänst för försäkran** och sedan i Azure-portalen under Atlassians molnet **SAML grundkonfiguration**, klistra in den i den **svars-URL**box. Inloggnings-URL:en är klient-URL:en för ditt Atlassian-moln.

    > [!NOTE]
    > Välj **Ja, uppdatera konfigurationen**, om du är en befintlig kund, när du har uppdaterat värdena för **SP-identitets-ID** och **URL för SP-konsumenttjänst för försäkran** i Azure Portal. Om du är en ny kund kan du hoppa över det här steget.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning om du beviljar åtkomst till Atlassians moln.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Atlassians molnet**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-atlassian-cloud-test-user"></a>Skapa testanvändare för Atlassian Cloud

Om du vill aktivera Azure AD-användare så att de kan logga in på Atlassian Cloud måste du etablera användarkontona manuellt i Atlassian Cloud genom att göra följande:

1. Välj **Användare** på **Administration**-panelen.

    ![Atlassian Cloud-användarlänken](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

2. Välj **Bjud in användare** om du vill skapa en användare i Atlassian Cloud.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

3. Ange användarens e-postadress i rutan **E-postadress** och tilldela sedan programmet åtkomst.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

4. Välj **Bjud in användare** om du vill skicka en e-postinbjudan till användaren. En e-postinbjudan skickas till användaren och efter det att användaren har accepterat inbjudan aktiveras hen i systemet.

> [!NOTE]
> Du kan också skapa flera användare genom att använda knappen **Skapa flera** i avsnittet **Användare**.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Atlassians molnet i åtkomstpanelen, bör det vara loggas in automatiskt till Atlassians molnet som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)