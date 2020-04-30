---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Trend Micro Web Security (TMWS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trend Micro Web Security (TMWS).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083469"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Trend Micro Web Security (TMWS)

I den här självstudien får du lära dig hur du integrerar Trend Micro Web Security (TMWS) med Azure Active Directory (Azure AD). När du integrerar Trend Micro Web Security (TMWS) med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Trend Micro Web Security (TMWS).
* Gör det möjligt för användarna att logga in automatiskt till Trend Micro Web Security (TMWS) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Trend Micro Web Security (TMWS)-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Trend Micro Web Security (TMWS) stöder **SP** -INITIERAd SSO
* När du har konfigurerat Trend Micro Web Security (TMWS) kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Lägga till Trend Micro Web Security (TMWS) från galleriet

Om du vill konfigurera integreringen av Trend Micro Web Security (TMWS) i Azure AD måste du lägga till Trend Micro Web Security (TMWS) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Trend Micro Web Security (TMWS)** i sökrutan.
1. Välj **Trend Micro Web Security (TMWS)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Konfigurera och testa enkel inloggning med Azure AD för Trend Micro Web Security (TMWS)

Konfigurera och testa Azure AD SSO med Trend Micro Web Security (TMWS) med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Trend Micro Web Security (TMWS).

Slutför följande Bygg stenar om du vill konfigurera och testa Azure AD SSO med Trend Micro Web Security (TMWS):

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
    1. **[Konfigurera inställningar för synkronisering av användare och grupper i Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** – konfigurera inställningar för synkronisering av användare och grupper i Azure AD
1. **[Konfigurera TMWS (Trend Micro Web Security) SSO](#configure-trend-micro-web-security-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering för **Trend Micro Web Security (TMWS)** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Skriv en URL i textrutan **Svars-URL**: `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Du kan hämta dessa värden under **tjänst leverantörs inställningarna för Azure Admin Portal** -avsnittet på skärmen **AUTENTISERINGSMETOD** för azure AD från **Administration > Directory Services**.

1. I TMWS-programmet (Trend Micro Web Security) förväntas SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig TMWS-program (Trend Micro Web Security) att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name | Källattribut|
    | --------------- | --------- |
    | sAMAccountName | User. egna namnet onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Trend Micro Web Security (TMWS)** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Trend Micro Web Security (TMWS).

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Trend Micro Web Security (TMWS)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurera inställningar för synkronisering av användare och grupper i Azure AD

1. Klicka på **Azure Active Directory**i det vänstra navigerings fältet.

1. Under **Hantera**klickar du på **Appregistreringar** och klickar sedan på ditt nya företags program under **alla program** .

1. Under **Hantera**, klickar du på **certifikat & hemligheter**.

1. Klicka på **ny klient hemlighet**under avsnittet klient hemligheter som visas.

1. På sidan Lägg till en klient hemlighet som visas kan du lägga till en beskrivning och välja en förfallo period för den här klient hemligheten och sedan klicka på **Lägg till**. Den nyligen tillagda klient hemligheten visas under avsnittet klient hemligheter.

1. Registrera värdet. Senare kommer du att skriva in informationen i TMWS.

1. Under **Hantera**, klickar du på **API-behörigheter**. 

1. På skärmen API-behörigheter som visas klickar du på **Lägg till en behörighet**.

1. På fliken Microsoft API: er i skärmen begär API-behörigheter som visas klickar du på **Microsoft Graph** och sedan på **program behörigheter**.

1. Leta upp och Lägg till följande behörigheter: 

    * Group.Read.All
    * User. Read. all

1. Klicka på **Lägg till behörigheter**. Ett meddelande visas för att bekräfta att inställningarna har sparats. De nyligen tillagda behörigheterna visas på skärmen API-behörigheter.

1. Under avsnittet beviljande medgivande klickar du på **bevilja administratörs medgivande för < administratörs kontot > (standard katalog)** och sedan **Ja**. Ett meddelande visas för att bekräfta att administratörs medgivande för de begärda behörigheterna har beviljats.

1. Klicka på **Översikt**. 

1. I den högra rutan som visas registrerar du program-ID och katalog (klient)-ID: t. Senare kommer du att skriva in informationen i TMWS. Du kan också klicka på **anpassade domän namn** under Azure **Active Directory > hantera** och registrera domän namnet i den högra rutan.

## <a name="configure-trend-micro-web-security-sso"></a>Konfigurera Trend Micro Web Security SSO

1. Logga in på TMWS-hanterings konsolen och gå till **administrations** > **användare &** > **Directory-tjänster**för autentisering.

1. Klicka här på den övre delen av skärmen.

1. På skärmen autentiseringsmetod som visas klickar du på **Azure AD**.

1. Klicka **på** eller **av** för att bestämma om du vill tillåta att AD-användare av din organisation besöker webbplatser via TMWS om deras data inte synkroniseras till TMWS.

    > [!NOTE]
    > Användare som inte har synkroniserats från Azure AD kan bara autentiseras via kända TMWS-gatewayer eller den dedikerade porten för din organisation.

1. Utför följande steg i avsnittet **Inställningar för identitetsprovider** :

    a. I fältet **tjänst-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal

    b. I fältet **inloggnings namn attribut** klistrar du in användar anspråkets namn med attributet **User. egna namnet onpremisessamaccountname** source från Azure Portal.

    c. I fältet **offentligt SSL-certifikat** använder du det nedladdade **certifikatet (Base64)** från Azure Portal.

1. Utför följande steg i avsnittet **Inställningar för synkronisering** :

    a. I fältet **innehavare** använder du **Directory (klient) ID** eller **anpassat domän namn** värde från Azure Portal.

    b. I fältet **program-ID** , ID-värde för **program (klient)** från Azure Portal.

    c. I fältet **klient hemlighet** använder du **klient hemlighet** från Azure Portal.

    d. I fältet **synkroniseringsschema** väljer du att synkronisera med Azure AD manuellt eller enligt ett schema. Om du väljer manuellt, när det finns ändringar i Active Directory användar information, kom ihåg att gå tillbaka till sidan katalog tjänster och utföra manuell synkronisering så att informationen i TMWS förblir aktuell.

    e. Klicka på **Testa anslutning** för att kontrol lera om Azure AD-tjänsten kan anslutas korrekt. 
    
    f. Klicka på **Spara**.
 
 > [!NOTE]
 > Mer information om hur du konfigurerar Trend Micro Web Security med Azure AD finns i [det här](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) dokumentet.

## <a name="test-sso"></a>Testa SSO 

När du har konfigurerat Azure AD-tjänsten och angett Azure AD som autentiseringsmetod för användaren, kan du logga in på TMWS-proxyservern för att verifiera installationen. När Azure AD-inloggningen verifierat ditt konto kan du besöka Internet.

> [!NOTE]
> TMWS stöder inte testning av enkel inloggning från Azure AD-portalen, under översikt > enkel inloggning > konfigurera enkel inloggning med SAML > test av ditt nya företags program.

1. Rensa webbläsaren för alla cookies och starta sedan om webbläsaren. 

1. Peka din webbläsare till TMWS-proxyservern. Mer information finns i [vidarebefordran av trafik med PAC-filer](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Besök en webbplats på Internet. TMWS leder dig till TMWS-portalen.

1. Ange ett Active Directory konto (format: domain\sAMAccountName eller sAMAccountName@domain) eller e-postadress eller UPN och klicka sedan på **Logga in**. TMWS skickar dig till Azure AD-inloggningen.

1. Ange autentiseringsuppgifterna för ditt AD-konto i Azure AD-inloggningen. Du ska kunna logga in på TMWS.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Trend Micro Web Security (TMWS) med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda Trend Micro Web Security (TMWS) med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

