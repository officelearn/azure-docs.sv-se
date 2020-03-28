---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Google Cloud (G Suite) Connector | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Google Cloud (G Suite) Connector.
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
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3282dd88b62a6811031e95672638d67702215a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048449"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Google Cloud (G Suite) Connector

I den här självstudien får du lära dig hur du integrerar Google Cloud (G Suite) Connector med Azure Active Directory (Azure AD). När du integrerar Google Cloud (G Suite) Connector med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Google Cloud (G Suite) Connector.
* Gör det möjligt för användarna att automatiskt logga in på Google Cloud (G Suite) Connector med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

- En Azure AD-prenumeration.
- Google Cloud (G Suite) Connector enkel inloggning (SSO) aktiverad prenumeration.
- En Google Apps-prenumeration eller Google Cloud Platform-prenumeration.

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien. Det här dokumentet har skapats med den nya användarupplevelsen Enkel inloggning. Om du fortfarande använder den gamla upplevelsen ser installationen annorlunda ut. Du kan aktivera den nya upplevelsen i inställningarna för enkel inloggning i G Suite-programmet. Gå till **Azure AD, Enterprise-program**, välj **Google Cloud (G Suite) Connector**, välj Enkel **inloggning** och klicka sedan på Prova vår **nya upplevelse**.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. **F: Stöder den här integreringen SSO-integreringen av Google Cloud Platform med Azure AD?**

    S: Ja. Google Cloud Platform och Google Apps delar samma autentiseringsplattform. Så om du vill göra GCP-integreringen måste du konfigurera enkel inloggning med Google Apps.

2. **F: Är Chromebook och andra Chrome-enheter kompatibla med enkel inloggning i Azure AD?**
  
    S: Ja, användare kan logga in på sina Chromebook-enheter med sina Azure AD-autentiseringsuppgifter. Se den här [supportartikeln för Google Cloud (G Suite) Connector](https://support.google.com/chrome/a/answer/6060880) för information om varför användare kan bli ombedda att ange autentiseringsuppgifter två gånger.

3. **F: Om jag aktiverar enkel inloggning, kommer användarna att kunna använda sina Azure AD-autentiseringsuppgifter för att logga in på någon Google-produkt, till exempel Google Classroom, Gmail, Google Drive, YouTube och så vidare?**

    S: Ja, beroende på [vilken Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) du väljer att aktivera eller inaktivera för din organisation.

4. **F: Kan jag aktivera enkel inloggning för endast en delmängd av mina Google Cloud-connector-användare(G Suite) ?**

    S: Nej, om du aktiverar enkel inloggning krävs omedelbart att alla Google Cloud(G Suite) Connector-användare autentiserar med sina Azure AD-autentiseringsuppgifter. Eftersom Google Cloud (G Suite) Connector inte stöder att ha flera identitetsleverantörer kan identitetsleverantören för google cloud (G Suite) Connector-miljön antingen vara Azure AD eller Google – men inte båda samtidigt.

5. **F: Om en användare är inloggad via Windows autentiseras de automatiskt till Google Cloud (G Suite) Connector utan att bli tillfrågad om ett lösenord?**

    S: Det finns två alternativ för att aktivera det här scenariot. Antingen kan användare logga in på Windows 10-enheter via [Azure Active Directory Join](../device-management-introduction.md). Alternativt kan användare logga in på Windows-enheter som är domänanslutna till en lokal Active Directory som har aktiverats för enkel inloggning till Azure AD via en [Active Directory Federation Services](../hybrid/plan-connect-user-signin.md)-distribution (AD FS). Båda alternativen kräver att du utför stegen i följande självstudie för att aktivera enkel inloggning mellan Azure AD och Google Cloud (G Suite) Connector.

6. **F: Vad ska jag göra när jag får felmeddelandet "ogiltigt e-postmeddelande"?**

    S: För den här inställningen krävs e-postattributet för att användarna ska kunna logga in. Det här attributet kan inte anges manuellt.

    E-postadressattribut fylls i automatiskt för alla användare med en giltig Exchange-licens. Om användaren inte är e-postaktiverad, får hen det här felet allteftersom programmet behöver få det här attributet för att ge åtkomst.

    Du kan gå till portal.office.com med ett administratörskonto och sedan i administrationscentret klicka på fakturering, prenumerationer, välja din Office 365-prenumeration och sedan klicka på tilldela till användare, välja de användare som du vill kontrollera prenumerationen för och i den högra rutan klicka på redigera licenser.

    När O365-licensen har tilldelats kan det ta några minuter innan den blir aktiv. Efter det kommer attributet user.mail att fyllas i automatiskt och problemet ska vara löst.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Google Cloud (G Suite) Connector stöder **SP-initierad** SSO

* Google Cloud (G Suite) Connector stöder [ **automatisk** användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* När du har konfigurerat Google Cloud (G Suite) Connector kan du framtvinga Sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Lägga till Google Cloud (G Suite) Connector från galleriet

Om du vill konfigurera integreringen av Google Cloud (G Suite) Connector i Azure AD måste du lägga till Google Cloud (G Suite) Connector från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Google Cloud (G Suite) Connector** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Google Cloud (G Suite) Connector** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Konfigurera och testa Azure AD enkel inloggning för Google Cloud (G Suite) Connector

Konfigurera och testa Azure AD SSO med Google Cloud (G Suite) Connector med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Google Cloud (G Suite) Connector.

Om du vill konfigurera och testa Azure AD SSO med Google Cloud (G Suite) Connector slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Google Cloud (G Suite) Connector SSO](#configure-google-cloud-g-suite-connector-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Google Cloud (G Suite) Connector-testanvändare](#create-google-cloud-g-suite-connector-test-user)** – om du vill ha en motsvarighet till B.Simon i Google Cloud (G Suite) Connector som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan **Google Cloud (G Suite)** **Connector-programintegration**och väljer enkel inloggning . **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera för **Gmail**:

    a. Skriv en URL i textrutan **Sign-on URL** med följande mönster:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster:  

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera för **Google Cloud Platform**:

    a. Skriv en URL i textrutan **Sign-on URL** med följande mönster:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. I textrutan **Svars-URL** skriver du en URL med följande mönster:  
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Google Cloud (G Suite) Connector tillhandahåller inte enhets-ID/identifier-värde på single sign on-konfiguration, så `google.com`när du avmarkerar det **domänspecifika utfärdaralternativet** blir identifierares värde . Om du kontrollerar **domänspecifika utfärdare** alternativet kommer det att vara `google.com/a/<yourdomainname.com>`. Om du vill kontrollera/avmarkera det **domänspecifika utfärdaralternativet** måste du gå till avsnittet **Konfigurera Google Cloud (G Suite) Connector SSO** som förklaras senare i självstudien. Kontakta [Supportteamet för Google Cloud (G Suite) Connector Client](https://www.google.com/contact/).

1. Google Cloud (G Suite) Connector-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar ett exempel på detta. Standardvärdet för **Unik användaridentifierare** är **userprincipalname,** men Google Cloud (G Suite) Connector förväntar sig att detta ska mappas med användarens e-postadress. Till det kan du använda **user.mail**-attributet från listan eller rätt attributvärde baserat på organisationens konfiguration.

    ![image](common/default-attributes.png)


1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Google Cloud (G Suite) kontakter.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Google Cloud (G Suite) Connector.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Google Cloud **(G Suite) Connector i**programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Konfigurera Google Cloud (G Suite) Connector SSO

1. Öppna en ny flik i webbläsaren och logga in på [Google Cloud (G Suite) Connector Admin Console](https://admin.google.com/) med ditt administratörskonto.

2. Klicka på **Säkerhet**. Om du inte ser länken, kan den vara dold under menyn **Fler kontroller** längst ned på skärmen.

    ![Klicka på Säkerhet.][10]

3. På sidan **Säkerhet** klickar du på **Konfigurera enkel inloggning (SSO).**

    ![Klicka på enkel inloggning.][11]

4. Utför följande konfigurationsändringar:

    ![Konfigurera enkel inloggning][12]

    a. Välj **Installera enkel inloggning med tredjeparts identitetsprovider**.

    b. I **url-fältet Inloggningssida i** Google Cloud (G Suite) Connector klistrar du in värdet **för inloggningsadressen** som du har kopierat från Azure-portalen.

    c. I **url-fältet För utloggningssida** i Google Cloud (G Suite) Connector klistrar du in värdet **för url för utloggning** som du har kopierat från Azure-portalen.

    d. I fältet **Ändra lösenordsadress** i Google Cloud (G Suite) Connector klistrar du in värdet **för Ändra lösenordsadress** som du har kopierat från Azure-portalen.

    e. Ladda upp certifikatet som du har hämtat från Azure-portalen för **verifieringscertifikatet**i Google Cloud (G Suite) Connector.

    f. Markera/avmarkera alternativet **Använd en domänspecifik utfärdare** enligt anteckningen som nämns i avsnittet **grundläggande SAML-konfiguration** i Azure AD.

    g. Klicka på **Spara ändringar**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Skapa testanvändare för Google Cloud (G Suite) Connector

Syftet med det här avsnittet är att [skapa en användare i Google Cloud (G Suite) Connector](https://support.google.com/a/answer/33310?hl=en) som heter B.Simon. När användaren har skapats manuellt i Google Cloud (G Suite) Connector kan användaren nu logga in med sina inloggningsuppgifter för Office 365.

Google Cloud (G Suite) Connector stöder också automatisk användaretablering. Om du vill konfigurera automatisk användaretablering måste du först [konfigurera Google Cloud (G Suite) Connector för automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Kontrollera att användaren redan finns i Google Cloud (G Suite) Connector om etableringen i Azure AD inte har aktiverats innan du testar Enkel inloggning.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta [Google-supportteamet](https://www.google.com/contact/).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på google cloud(G Suite) anslutningspanelen på åtkomstpanelen bör du automatiskt loggas in på Google Cloud (G Suite) Connector som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera etablering av användare](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Prova Google Cloud (G Suite) Connector med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Google Cloud (G Suite) Connector med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png