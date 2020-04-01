---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Firmex VDR | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Firmex VDR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 670ff192-c23e-49e4-8fd1-516e02d8856c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfb857d3a68081fda84aef33e6b5a4b4d1bce28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76761240"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Firmex VDR

I den här självstudien får du lära dig hur du integrerar Firmex VDR med Azure Active Directory (Azure AD). När du integrerar Firmex VDR med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Firmex VDR.
* Gör att användarna automatiskt loggas in på Firmex VDR med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Firmex VDR enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Firmex VDR stöder **SP och IDP** initierade SSO

* När du har konfigurerat Firmex kan du framtvinga sessionskontroller som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Lägga till Firmex VDR från galleriet

Om du vill konfigurera integreringen av Firmex VDR i Azure AD måste du lägga till Firmex VDR från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Firmex VDR** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Firmex VDR** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Konfigurera och testa en azure AD-inloggning för Firmex VDR

Konfigurera och testa Azure AD SSO med Firmex VDR med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Firmex VDR.

Så här konfigurerar och testar du Azure AD SSO med Firmex VDR:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Firmex VDR SSO](#configure-firmex-vdr-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Firmex VDR-testanvändare](#create-firmex-vdr-test-user)** - om du vill ha en motsvarighet till B.Simon i Firmex VDR som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet **Hantera** på sidan Hantera på sidan Firmex VDR-program och välj enkel inloggning på sidan **Firmex VDR-programintegration** . **single sign-on**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://login.firmex.com`

1. Klicka på **Spara**.

1. Firmex VDR-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Firmex VDR-program att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ------------ | --------- |
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Firmex VDR** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Firmex VDR.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Firmex VDR**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-firmex-vdr-sso"></a>Konfigurera Firmex VDR SSO

### <a name="before-you-get-started"></a>Innan du börjar

#### <a name="what-youll-need"></a>Vad du behöver

-   En aktiv Firmex-prenumeration
-   Azure AD som SSO-tjänst
-   IT-administratören konfigurerar SSO
-   När SSO är aktiverat måste alla företagets användare logga in på Firmex med SSO och inte använda ett inloggnings-/lösenord.

#### <a name="how-long-will-this-take"></a>Hur lång tid tar det?

Implementering av SSO tar några minuter. Det finns praktiskt taget inga driftstopp mellan Firmex Support som aktiverar SSO för din webbplats och företagets användare som autentiserar med SSO. Följ bara stegen nedan.

### <a name="step-1---identify-your-companys-domains"></a>Steg 1 - Identifiera företagets domäner

Identifiera de domäner som företagets användare loggar in med.

Ett exempel:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Steg 2 - Kontakta Firmex Support med dina domäner

E-post [Firmex Support Team](mailto:support@firmex.com) eller ring 1888 688 4042 x.11 att tala med Firmex Support. Skicka längs din domäninformation. Firmex Support lägger till domänerna i din VDR som **påstådda domäner**. Administratören måste nu konfigurera SSO.

Varning: Tills webbplatsadministratören konfigurerar de påstådda domänerna kan företagets användare inte logga in på VDR. Icke-företagsanvändare (det vill än gästanvändare) kan fortfarande logga in med sin e-postadress/sitt lösenord. Konfigurationen bör ta några minuter.

### <a name="step-3---configure-the-claimed-domains"></a>Steg 3 - Konfigurera de domäner som begärs

1. Logga in på Firmex som webbplatsadministratör.
1. Klicka på företagets logotyp i det övre vänstra hörnet.
1. Välj fliken **SSO.** Välj sedan **SSO-konfiguration**. Klicka på den domän som du vill konfigurera.

    ![Påstådda domäner](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Låt IT-administratören fylla i följande fält. Fälten ska hämtas från din identitetsleverantör:  

    ![SSO-konfiguration](./media/firmex-vdr-tutorial/SSO-config.png)

    a. I textrutan **Entitets-ID** klistrar du in azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    b. I textrutan **URL för identitetsprovider** klistrar du in värdet **för inloggnings-URL,** som du har kopierat från Azure-portalen.

    c. **Certifikat för offentlig nyckel** - I autentiseringssyfte kan ett SAML-meddelande signeras digitalt av utfärdaren. För att verifiera signaturen i meddelandet använder meddelandemottagaren en offentlig nyckel som är känd för att tillhöra utfärdaren. På samma sätt måste en offentlig krypteringsnyckel som tillhör den slutliga mottagaren vara känd för utfärdaren för att kryptera ett meddelande. I båda situationerna – signering och kryptering – måste betrodda offentliga nycklar delas i förväg.  Detta är **X509Certificate** från **Federation Metadata XML**

    d. Klicka på **Spara** för att slutföra SSO-konfigurationen. Ändringarna träder i kraft omedelbart.

1. För närvarande är SSO aktiverat för din webbplats.

### <a name="create-firmex-vdr-test-user"></a>Skapa Firmex VDR-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Firmex. Arbeta med [Firmex supportteam](mailto:support@firmex.com) för att lägga till användarna i Firmex-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Firmex VDR på åtkomstpanelen ska du automatiskt loggas in på den Firmex VDR som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Firmex VDR med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar Firmex med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
