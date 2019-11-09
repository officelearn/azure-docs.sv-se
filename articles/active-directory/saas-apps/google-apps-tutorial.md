---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med G Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d8e13a4e042146ef2b99728e41e14f1dcb3435
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885360"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med G Suite

I den här självstudien får du lära dig hur du integrerar G Suite med Azure Active Directory (Azure AD). När du integrerar G Suite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till G Suite.
* Gör det möjligt för användarna att logga in automatiskt till G Suite med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

- En Azure AD-prenumeration.
- G Suite-aktiverad prenumeration med enkel inloggning (SSO).
- En Google Apps-prenumeration eller Google Cloud Platform-prenumeration.

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien. Det här dokumentet har skapats med den nya användarupplevelsen Enkel inloggning. Om du fortfarande använder den gamla upplevelsen ser installationen annorlunda ut. Du kan aktivera den nya upplevelsen i inställningarna för enkel inloggning i G Suite-programmet. Gå till **Azure AD, företagsprogram**, välj **G Suite**, välj **enkel inloggning** och klicka sedan på **Testa vår nya upplevelse**.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. **F: stöder denna integrering Google Cloud Platform SSO-integrering med Azure AD?**

    S: Ja. Google Cloud Platform och Google Apps delar samma autentiseringsplattform. Så om du vill göra GCP-integreringen måste du konfigurera enkel inloggning med Google Apps.

2. **F: är Chromebooks och andra Chrome-enheter kompatibla med enkel inloggning med Azure AD?**
  
    A: Ja, användarna kan logga in på sina Chromebook-enheter med sina autentiseringsuppgifter för Azure AD. Se den här [G Suite-supportartikeln](https://support.google.com/chrome/a/answer/6060880) för information om varför användare kan tillfrågas om autentiseringsuppgifter två gånger.

3. **F: om jag aktiverar enkel inloggning kommer användarna att kunna använda sina Azure AD-autentiseringsuppgifter för att logga in på en Google-produkt, till exempel Google Classroom, GMail, Google Drive, YouTube och så vidare?**

    S: Ja, beroende på [vilken G-Svit](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) du väljer att aktivera eller inaktivera för din organisation.

4. **F: kan jag aktivera enkel inloggning för endast en del av mitt G Suite-användare?**

    S: Nej, om du aktiverar enkel inloggning kräver omedelbart att alla användare i G Suite autentiseras med sina autentiseringsuppgifter för Azure AD. Eftersom G Suite inte har stöd för att ha flera identitetsproviders, kan identitetsleverantören för din G Suite-miljö antingen vara Azure AD eller Google – men inte båda på samma gång.

5. **F: om en användare är inloggad via Windows autentiseras de automatiskt till G Suite utan att du behöver ange ett lösen ord?**

    A: det finns två alternativ för att aktivera det här scenariot. Antingen kan användare logga in på Windows 10-enheter via [Azure Active Directory Join](../device-management-introduction.md). Alternativt kan användare logga in på Windows-enheter som är domänanslutna till en lokal Active Directory som har aktiverats för enkel inloggning till Azure AD via en [Active Directory Federation Services](../hybrid/plan-connect-user-signin.md)-distribution (AD FS). I båda fallen måste du utföra stegen i följande självstudie för att aktivera enkel inloggning mellan Azure AD och G Suite.

6. **F: Vad gör jag om jag får fel meddelandet "Ogiltigt e-postmeddelande"?**

    A: för den här installationen krävs e-postattributet för att användarna ska kunna logga in. Det här attributet kan inte anges manuellt.

    E-postadressattribut fylls i automatiskt för alla användare med en giltig Exchange-licens. Om användaren inte är e-postaktiverad, får hen det här felet allteftersom programmet behöver få det här attributet för att ge åtkomst.

    Du kan gå till portal.office.com med ett administratörskonto och sedan i administrationscentret klicka på fakturering, prenumerationer, välja din Office 365-prenumeration och sedan klicka på tilldela till användare, välja de användare som du vill kontrollera prenumerationen för och i den högra rutan klicka på redigera licenser.

    När O365-licensen har tilldelats kan det ta några minuter innan den blir aktiv. Efter det kommer attributet user.mail att fyllas i automatiskt och problemet ska vara löst.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* G Suite har stöd för **SP**-initierad enkel inloggning

* G Suite stöder [ **automatiserad** användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

## <a name="adding-g-suite-from-the-gallery"></a>Att lägga till G Suite från galleriet

Om du vill konfigurera integreringen av G Suite till Azure AD, behöver du lägga till G Suite från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **G Suite** i sökrutan.
1. Välj **G Suite** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Konfigurera och testa enkel inloggning med Azure AD för G Suite

Konfigurera och testa Azure AD SSO med G Suite med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i G Suite.

Om du vill konfigurera och testa Azure AD SSO med G Suite slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera G Suite SSO](#configure-g-suite-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa G Suite-test användare](#create-g-suite-test-user)** – för att få en motsvarighet till B. Simon i G Suite som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **G Suite** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera för **Gmail**:

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera för **Google Cloud Platform**:

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. G Suite tillhandahåller inte enhets-ID/ID-värde för enkel inloggning, så när du avmarkerar alternativet för **domän information** visas värdet för identifieraren `google.com`. Om du markerar alternativet för **domänbaserad utfärdare** kommer det att `google.com/a/<yourdomainname.com>`. Om du vill kontrol lera/avmarkera alternativet för **domänanslutet utfärdare** måste du gå till avsnittet **Konfigurera G Suite SSO** , som beskrivs senare i självstudien. Mer information om [support teamet för G Suite-klienten](https://www.google.com/contact/).

1. Ditt G Suite-program förväntar sig att SAML-intygen är i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. Följande skärmbild visar ett exempel på detta. Standardvärdet för **Unik användaridentifierare** är **user.userprincipalname**, men G Suite förväntar sig att detta mappas med användarens e-postadress. Till det kan du använda **user.mail**-attributet från listan eller rätt attributvärde baserat på organisationens konfiguration.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn | Källattribut |
    | ---------------| --------------- |
    | Unik användaridentifierare | User.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera G Suite** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till G Suite.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **G Suite**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-g-suite-sso"></a>Konfigurera G Suite SSO

1. Öppna en ny flik i webbläsaren och logga in på [G Suite-administratörskonsolen](https://admin.google.com/) med ditt administratörskonto.

2. Klicka på **Säkerhet**. Om du inte ser länken, kan den vara dold under menyn **Fler kontroller** längst ned på skärmen.

    ![Klicka på Säkerhet.][10]

3. På sidan **Säkerhet** klickar du på **Konfigurera enkel inloggning (SSO).**

    ![Klicka på enkel inloggning.][11]

4. Utför följande konfigurationsändringar:

    ![Konfigurera enkel inloggning][12]

    a. Välj **Installera enkel inloggning med tredjeparts identitetsprovider**.

    b. I fältet **inloggnings sidans URL** i G Suite klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. I fältet utloggnings **sidans URL** i G Suite klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure Portal.

    d. I fältet **ändra lösen ordets URL** i G Suite klistrar du in värdet för **ändra lösen ordets URL** som du har kopierat från Azure Portal.

    e. I G Suite för **verifieringscertifikatet**, laddar du upp det certifikat som du har hämtat från Azure Portal.

    f. Markera/avmarkera alternativet **Använd ett domänbaserat Issuer** -alternativ enligt kommentaren ovan i avsnittet **grundläggande SAML-konfiguration** i Azure AD.

    g. Klicka på **Spara ändringar**.

### <a name="create-g-suite-test-user"></a>Skapa G Suite-testanvändare

Syftet med det här avsnittet är att [skapa en användare i G Suite](https://support.google.com/a/answer/33310?hl=en) som kallas B. Simon. När användaren har skapats manuellt i G Suite kommer användaren nu att kunna logga in med sina inloggnings uppgifter för Office 365.

G Suite har även stöd för automatisk användar etablering. Om du vill konfigurera automatisk användar etablering måste du först [Konfigurera G Suite för automatisk användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Kontrollera att användaren redan finns i G Suite om etablering i Azure AD inte har aktiverats innan du testar enkel inloggning.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta [Google-supportteamet](https://www.google.com/contact/).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på G Suite-panelen i åtkomstpanelen så borde du automatiskt loggas in på den G Suite som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Konfigurera användarförsörjning](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Testa G Suite med Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
