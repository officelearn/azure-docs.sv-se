---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Trend Micro Web Security (TMWS) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trend Micro Web Security(TMWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083469"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Trend Micro Web Security(TMWS)

I den här självstudien får du lära dig hur du integrerar Trend Micro Web Security (TMWS) med Azure Active Directory (Azure AD). När du integrerar Trend Micro Web Security(TMWS) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Trend Micro Web Security(TMWS).
* Gör att användarna automatiskt loggas in på Trend Micro Web Security (TMWS) med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Trend Micro Web Security (TMWS) enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Trend Micro Web Security (TMWS) stöder **SP** initierade SSO
* När du har konfigurerat Trend Micro Web Security (TMWS) kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Lägga till Trend Micro Web Security (TMWS) från galleriet

Om du vill konfigurera integreringen av Trend Micro Web Security(TMWS) i Azure AD måste du lägga till Trend Micro Web Security(TMWS) från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Trend Mikro webbsäkerhet(TMWS)** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Trend Micro Web Security(TMWS)** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Konfigurera och testa en enda Azure AD-inloggning för Trend Micro Web Security(TMWS)

Konfigurera och testa Azure AD SSO med Trend Micro Web Security(TMWS) med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Trend Micro Web Security(TMWS).

Så här konfigurerar och testar du Azure AD SSO med Trend Micro Web Security(TMWS) slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
    1. **[Konfigurera inställningar för synkronisering av användare och grupper i Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** - Konfigurera inställningar för synkronisering av användare och grupper i Azure AD
1. **[Konfigurera Trend Micro Web Security(TMWS) SSO](#configure-trend-micro-web-security-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet Hantera på sidan Trend Micro Web Security(TMWS) på sidan **Integrering av** program för Trend **Mikro webbsäkerhet(TMWS)** och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Skriv en URL i textrutan **Svars-URL**: `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Du kan hämta dessa värden under **tjänstproviderinställningarna för** Azure Admin Portal-området på skärmen **Autentiseringsmetod** för Azure AD från **Administration > Directory Services**.

1. Trend Micro Web Security(TMWS) programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar till din SAML token attribut konfiguration. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan, Trend Micro Web Security (TMWS) ansökan förväntar sig att få fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.
    
    | Namn | Källattribut|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | Upn | user.userprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Trend Micro Web Security(TMWS)** baserat på dina krav.

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Trend Micro Web Security(TMWS).

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Trend Mikro **webbsäkerhet(TMWS)** i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurera inställningar för synkronisering av användare och grupper i Azure AD

1. Klicka på Azure Active Directory från den vänstra **navigeringen**.

1. Klicka på **Appregistreringar** under **Hantera**och klicka sedan på ditt nya företagsprogram under området **Alla program.**

1. Klicka på **Certifikat & hemligheter under** **Hantera.**

1. Klicka på **Ny klienthemlighet**under området Klienthemligheter som visas.

1. På skärmen Lägg till en klienthemlig skärm som visas kan du lägga till en beskrivning och välja en förfalloperiod för den här klienthemligheten och klicka sedan på **Lägg till**. Den nyligen tillagda klienthemligheten visas i området Klienthemligheter.

1. Registrera värdet. Senare skriver du informationen i TMWS.

1. Klicka på **API-behörigheter**under **Hantera.** 

1. Klicka på Lägg till en **behörighet**på skärmen API-behörigheter som visas .

1. Klicka på **Microsoft Graph** och sedan **programbehörigheter**på fliken Microsoft API i skärmen Begär API-behörigheter som visas.

1. Leta upp och lägg till följande behörigheter: 

    * Group.Read.All
    * Användare.Läs.Alla

1. Klicka på **Lägg till behörigheter**. Ett meddelande visas som bekräftar att inställningarna har sparats. De nyligen tillagda behörigheterna visas på API-behörighetsskärmen.

1. Under området Bevilja medgivande klickar du på **Bevilja administratörsmedgivande för < ditt administratörskonto > (standardkatalog)** och sedan **ja**. Ett meddelande verkar bekräfta att administratörens medgivande för de begärda behörigheterna har beviljats.

1. Klicka på **Översikt**. 

1. I den högra rutan som visas registrerar du program-ID:t och katalog-ID:t (klient). Senare skriver du informationen i TMWS. Du kan också klicka på **Anpassade domännamn** under Azure Active Directory **> Hantera** och registrera domännamnet i den högra rutan.

## <a name="configure-trend-micro-web-security-sso"></a>Konfigurera Trend Micro Web Security SSO

1. Logga in på TMWS-hanteringskonsolen och gå till **Administration** > **USERS & AUTHENTICATION** > **Directory Services**.

1. Klicka här på den övre delen av skärmen.

1. Klicka på **Azure AD**på skärmen Autentiseringsmetod .

1. Klicka **på På** eller **Av** för att bestämma om AD-användarna i din organisation ska tillåtas att besöka webbplatser via TMWS om deras data inte synkroniseras med TMWS.

    > [!NOTE]
    > Användare som inte synkroniseras från Azure AD kan autentiseras endast via kända TMWS-gateways eller den dedikerade porten för din organisation.

1. Gör följande i avsnittet **Inställningar för identitetsprovider:**

    a. I fältet **Tjänst-URL** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen

    b. I **attributfältet Inloggningsnamn** klistrar du in användaranspråksnamnet med källattributet **user.onpremisessamaccountname** från Azure-portalen.

    c. I fältet **Offentligt SSL-certifikat** använder du det hämtade **certifikatet (Base64)** från Azure-portalen.

1. Gör följande i avsnittet **Synkroniseringsinställningar:**

    a. I fältet **Klient** använder du **katalog-ID eller** **anpassat domännamnsvärde** från Azure-portalen.

    b. I fältet **Program-ID,** **Application (klient) ID-värde** från Azure-portalen.

    c. Använd **klienthemlighet** från Azure-portalen i fältet **Klienthemlighet.**

    d. Välj att synkronisera med Azure AD manuellt eller enligt ett schema i fältet **Synkroniseringsschema.** Om du väljer Manuellt, när det finns ändringar i Active Directory-användarinformation, kom ihåg att gå tillbaka till katalogtjänstskärmen och utföra manuell synkronisering så att informationen i TMWS förblir aktuell.

    e. Klicka på **Testa anslutning** för att kontrollera om Azure AD-tjänsten kan anslutas. 
    
    f. Klicka på **Spara**.
 
 > [!NOTE]
 > Mer information om hur du konfigurerar Trend Micro Web Security med Azure AD finns [i det här](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) dokumentet.

## <a name="test-sso"></a>Testa SSO 

När du har konfigurerat Azure AD-tjänsten och angett Azure AD som användarens autentiseringsmetod kan du logga in på TMWS-proxyservern för att verifiera din konfiguration. När Azure AD-inloggningen har verifierat ditt konto kan du besöka Internet.

> [!NOTE]
> TMWS stöder inte testning av enkel inloggning från Azure AD-portalen under Översikt > Enkel inloggning > Konfigurera enkel inloggning med SAML > Test av ditt nya företagsprogram.

1. Rensa webbläsaren för alla cookies och starta sedan om webbläsaren. 

1. Peka din webbläsare på TMWS proxyserver. Mer information finns i [Vidarebefordran av trafik med PAC-filer](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Besök någon Webbplats på Internet. TMWS leder dig till TMWS-captiveportalen.

1. Ange ett Active Directory-konto (format: domän\sAMAccountName eller sAMAccountName@domain), eller e-postadress eller UPN och klicka sedan på Logga **in**. TMWS skickar dig till Azure AD-inloggningen.

1. Skriv dina AD-kontouppgifter på Azure AD-inloggningen. Du bör logga in på TMWS.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Trend Micro Web Security(TMWS) med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Trend Micro Web Security (TMWS) med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

