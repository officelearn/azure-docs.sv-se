---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Cisco Webex-möten | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex Meetings.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e497c556bde1be4e498cd85a68282a0e3b2666
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Cisco Webex-möten

I den här självstudien får du lära dig hur du integrerar Cisco Webex-möten med Azure Active Directory (Azure AD). När du integrerar Cisco Webex-möten med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco Webex-möten.
* Gör det möjligt för användarna att automatiskt loggas in på Cisco Webex-möten med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Cisco Webex Meetings enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Cisco Webex Meetings stöder **SP och IDP** initierade SSO

* Cisco Webex-möten stöder just **in time-användares** etablering

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Lägga till Cisco Webex-möten från galleriet

Om du vill konfigurera integreringen av Cisco Webex-möten i Azure AD måste du lägga till Cisco Webex-möten från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Cisco Webex-möten** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Cisco Webex Meetings** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Konfigurera och testa en azure AD-inloggning för Cisco Webex-möten

Konfigurera och testa Azure AD SSO med Cisco Webex Meetings med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cisco Webex Meetings.

Om du vill konfigurera och testa Azure AD SSO med Cisco Webex Meetings slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera Cisco Webex Meetings SSO](#configure-cisco-webex-meetings-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Cisco Webex Meetings testanvändare](#create-cisco-webex-meetings-test-user)** - om du vill ha en motsvarighet till B.Simon i Cisco Webex-möten som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet Hantera på sidan Programintegrering av Cisco Webex Meetings på sidan **Programintegrering av** **Cisco Webex Meetings** och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** kan du konfigurera programmet i **IDP-initierat** läge genom att ladda upp **metadatafilen för Tjänsteleverantören** enligt följande:

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen för Tjänst providern har slutförts fylls **URL-värdena identifierare** och **svar** i i fyllas i i i grundläggande **SAML-konfiguration.**

    >[!Note]
    >Du kommer att få tjänsten Provider Metadata fil från **Konfigurera Cisco Webex Möten SSO** avsnitt, som förklaras senare i handledningen. 

1. Om du vill konfigurera programmet i **återupptastartat SP-läge** utför du följande steg:  

    a. Klicka på redigerings-/pennikonen i avsnittet **Grundläggande SAML-konfiguration.**

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)
    
    b. Skriv URL-adressen i textrutan **Logga in** med följande mönster:` https://<customername>.my.webex.com`

5. Cisco Webex Meetings-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig Cisco Webex Meetings ansökan få fler attribut som ska skickas tillbaka i SAML svar. I avsnittet Användaranspråk i dialogrutan Användarattribut utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn | Källattribut|
    | ---------------|  --------- |
    |   förnamn    | user.givenname |
    |   efternamn    | user.surname |
    |   e-post       | user.mail |
    |   uid    | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Välj **det** attributvärde som visas för den raden i listrutan i attributlistan källa.

    f. Klicka på **Spara**.

4. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Cisco Webex-möten** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cisco Webex-möten.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Cisco Webex Meetings**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurera SSO för Cisco Webex-möten

1. Gå `https://<customername>.webex.com/admin` till URL med dina administrationsuppgifter.

2. Gå till **Vanliga webbplatsinställningar** och navigera till **SSO-konfiguration**.
 
    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Gör följande på sidan **Webex Administration:**

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. välj **SAML 2.0** som **Federation Protocol**.

    b. Klicka på **Länken Importera SAML-metadata** för att ladda upp metadatafilen som du har hämtat från Azure-portalen.

    c. Klicka på **knappen Exportera** för att hämta metadatafilen för Tjänsteleverantören och ladda upp den i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    d. Skriv `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` och om du vill aktivera MFA med Azure AD i textrutan **AuthContextClassRef**`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Välj **Skapande av automatiskt konto**.

    >[!NOTE]
    >För att aktivera **just-in-time** användaretablering måste du kontrollera **skapandet av automatiskt konto**. Förutom att SAML token attribut måste skickas i SAML svar.

    f. Klicka på **Spara**.

    >[!NOTE]
    >Den här konfigurationen är endast avsedd för kunder som använder Webex UserID i e-postformat.

### <a name="create-cisco-webex-meetings-test-user"></a>Skapa testanvändare för Cisco Webex-möten

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i Cisco Webex Meetings. Cisco Webex Meetings stöder **just-in-time-etablering,** vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Cisco Webex Meetings skapas en ny när du försöker komma åt Cisco Webex-möten.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Cisco Webex Meetings på åtkomstpanelen bör du automatiskt loggas in på de Cisco Webex-möten som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ServiceNow med Azure AD](https://aad.portal.azure.com)
