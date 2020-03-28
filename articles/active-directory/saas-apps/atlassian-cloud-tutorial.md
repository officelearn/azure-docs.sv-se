---
title: 'Självstudiekurs: Azure Active Directory-integrering med Atlassian Cloud | Microsoft-dokument'
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
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a54b096019e9e38bc800ae313016a430062dab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74964335"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Självstudiekurs: Integrera Atlassian Cloud med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Atlassian Cloud med Azure Active Directory (Azure AD). När du integrerar Atlassian Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Atlassian Cloud.
* Gör att användarna automatiskt loggas in i Atlassian Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Atlassian Cloud enkel inloggning (SSO) aktiverad prenumeration.
* Om du vill aktivera enkel inloggning med SAML för Atlassian Cloud-produkter måste du konfigurera Atlassian-åtkomst. Läs mer om [Atlassians-åtkomst]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. 

* Atlassian Cloud stöder **SP- och IDP**-initierad enkel inloggning
* Atlassian Cloud stöder [automatisk etablering och avetablering av användare](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Lägga till Atlassian Cloud från galleriet

Om du vill konfigurera integreringen av Atlassian Cloud i Azure AD måste du lägga till Atlassian Cloud från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Atlassian Cloud** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Atlassian Cloud** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Atlassian Cloud med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Atlassian Cloud.

Så här konfigurerar och testar du Azure AD SSO med Atlassian Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Atlassian Cloud SSO](#configure-atlassian-cloud-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Atlassian Cloud-testanvändare](#create-atlassian-cloud-test-user)** – om du vill ha en motsvarighet till B.Simon i Atlassian Cloud som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan **Integrering av Atlassian** **Cloud-program**hittar du avsnittet **Hantera** och väljer Enkel inloggning .
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://auth.atlassian.com/saml/<unique ID>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Föregående värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Du kommer att få dessa verkliga värden från **Atlassian Cloud SAML Konfiguration** skärmen som förklaras senare i **Konfigurera Atlassian Cloud Single Sign-On** av handledning.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<instancename>.atlassian.net`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Klistra in värdet från den instans som du använder för att logga in på administratörsportalen för Atlassian Cloud.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Ditt Atlassian Cloud-program förväntar sig SAML-intygen i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Atlassian Cloud-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Atlassian Cloud** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Atlassian Cloud.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Atlassian Cloud**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-atlassian-cloud-sso"></a>Konfigurera Atlassian Cloud SSO

1. Om du vill automatisera konfigurationen i Atlassian Cloud måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Setup Atlassian Cloud** kommer att leda dig till Atlassian Cloud ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Atlassian Cloud. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera Atlassian Cloud manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen Atlassian Cloud som administratör och utför följande steg:

1. Du måste verifiera din domän innan du fortsätter med att konfigurera enkel inloggning. Mer information finns i dokumentet [Atlassian-domänverifiering](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

1. Välj > **Säkerhets-SAML-inloggning**i den vänstra rutan . **Security** Prenumerera på Atlassian Identity Manager om du inte redan gör det.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Gör följande i fönstret **Lägg till SAML-konfiguration**:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. I rutan **Identitetsprovidernhets-ID** klistrar du in **Azure AD-identifieraren** som du kopierade från Azure-portalen.

    b. I rutan **SSO-url för identitetsprovider** klistrar du in **inloggnings-URL:en** som du kopierade från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure Portal i en txt-fil, kopiera värdet (utan raderna *Starta certifikat* och *Avsluta certifikat*) och klistra in det i rutan **Offentligt X509-certifikat**.

    d. Klicka på **Spara konfiguration**.

1. Säkerställ att du har konfigurerat rätt URL:er genom att uppdatera Azure AD-inställningarna på följande sätt:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. I SAML-fönstret kopierar du **SP-identitets-ID:et** och klistrar sedan in det i rutan **Identifierare** i Azure-portalen under Atlassian Cloud **Basic SAML-konfiguration.**

    b. I SAML-fönstret kopierar du **URL:en** för SP Assertion Consumer Service och klistrar sedan in den i rutan Svars-URL i rutan **Svars-URL** i Azure-portalen under Atlassian Cloud **Basic SAML-konfiguration.** Inloggnings-URL:en är klient-URL:en för ditt Atlassian-moln.

    > [!NOTE]
    > Välj **Ja, uppdatera konfigurationen**, om du är en befintlig kund, när du har uppdaterat värdena för **SP-identitets-ID** och **URL för SP-konsumenttjänst för försäkran** i Azure Portal. Om du är en ny kund kan du hoppa över det här steget.

### <a name="create-atlassian-cloud-test-user"></a>Skapa testanvändare för Atlassian Cloud

Om du vill aktivera Azure AD-användare så att de kan logga in på Atlassian Cloud måste du etablera användarkontona manuellt i Atlassian Cloud genom att göra följande:

1. Välj **Användare** på **Administration**-panelen.

    ![Atlassian Cloud-användarlänken](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Välj **Bjud in användare** om du vill skapa en användare i Atlassian Cloud.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Ange användarens e-postadress i rutan **E-postadress** och tilldela sedan programmet åtkomst.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Välj **Bjud in användare** om du vill skicka en e-postinbjudan till användaren. En e-postinbjudan skickas till användaren och efter det att användaren har accepterat inbjudan aktiveras hen i systemet.

> [!NOTE]
> Du kan också skapa flera användare genom att använda knappen **Skapa flera** i avsnittet **Användare**.

### <a name="test-sso"></a>Testa SSO

När du väljer atlassiska molnpanelen på åtkomstpanelen ska du automatiskt loggas in i atlassianska molnet som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Atlassian Cloud med Azure AD](https://aad.portal.azure.com/)
