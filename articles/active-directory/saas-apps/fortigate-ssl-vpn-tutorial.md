---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med FortiGate SSL VPN | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med FortiGate SSL VPN

I den här självstudien får du lära dig hur du integrerar FortiGate SSL VPN med Azure Active Directory (Azure AD). När du integrerar FortiGate SSL VPN med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till FortiGate SSL VPN.
* Gör det möjligt för användarna att logga in automatiskt för att FortiGate SSL VPN med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* FortiGate för SSL VPN, enkel inloggning (SSO) med aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* FortiGate SSL VPN stöder **SP** -INITIERAd SSO
* När du har konfigurerat FortiGate SSL VPN kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Lägga till FortiGate SSL VPN från galleriet

Om du vill konfigurera integreringen av FortiGate SSL VPN i Azure AD måste du lägga till FortiGate SSL VPN från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Fortigate SSL VPN** i sökrutan.
1. Välj **FORTIGATE SSL VPN** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Konfigurera och testa Azure AD SSO för FortiGate SSL VPN

Konfigurera och testa Azure AD SSO med FortiGate SSL VPN med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i FortiGate SSL VPN.

Om du vill konfigurera och testa Azure AD SSO med FortiGate SSL VPN, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FORTIGATE SSL VPN SSO](#configure-fortigate-ssl-vpn-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **Skapa FORTIGATE SSL VPN test User** – om du vill ha en motsvarighet till B. Simon i FORTIGATE SSL VPN som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-single-sign-on)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Fortigate SSL VPN-** program integration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<FQDN>/remote/login`

    b. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<FQDN>/remote/saml/metadata`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://><FQDN/remote/saml/login`

    d. I text rutan **utloggnings-URL** skriver du en URL med följande mönster: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med URL: en för faktisk inloggning, identifierare, svars-URL och utloggnings-URL. Kontakta [Fortigate för SSL VPN-support](mailto:tac_amer@fortinet.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. FortiGate SSL VPN-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig FortiGate SSL VPN-program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ------------ | --------- |
    | användarnamn | user.userprincipalname |
    | group | användare. grupper |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera FORTIGATE SSL-VPN** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till FortiGate SSL VPN.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **FORTIGATE SSL VPN**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-a-security-group-for-the-test-user"></a>Skapa en säkerhets grupp för test användaren

I det här avsnittet ska du skapa en säkerhets grupp i Azure Active Directory för test användaren. Den här säkerhets gruppen används av FortiGate för att ge användaren åtkomst till nätverket via VPN.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**och väljer sedan **grupper**.
1. Välj **ny grupp** överst på skärmen.
1. I de **nya grupp** egenskaperna följer du dessa steg:
   1. I fältet **typ av grupp** väljer du **säkerhet**.
   1. I **Namn**-fältet skriver du `FortiGateAccess`.
   1. I fältet **grupp Beskrivning** anger du `Group for granting FortiGate VPN access` .
   1. För **Azure AD-roller kan tilldelas gruppen (förhands granskning)** , väljer du **Nej**.
   1. I fältet **medlemskaps typ** väljer du **tilldelad**.
   1. Under **medlemmar**väljer du **inga medlemmar markerade**.
   1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
   1. Välj **Skapa**.
1. När du har kommit tillbaka till bladet **grupper** i Azure Active Directory letar du upp **åtkomst gruppen Fortigate** och noterar **objekt-ID** för senare användning.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurera FortiGate SSL VPN SSO

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Överför base64 SAML-certifikatet till FortiGate-enheten

När du har slutfört SAML-konfigurationen av FortiGate-appen i din klient, hämtade du det Base64-kodade SAML-certifikatet. Detta måste överföras till FortiGate-enheten:

1. Logga in på hanterings portalen för FortiGate-enheten.
1. I den vänstra menyn klickar du på **system**.
1. Klicka på **certifikat**under **system**.
1. Klicka på **Importera**  ->  **fjärrcertifikat**.
1. Bläddra till det certifikat som har laddats ned från FortiGate app Deployment i Azure-klienten, markera det och klicka på **OK**

När certifikatet har överförts noterar du dess namn under **system**  >  **certifikatets**  >  **fjärrcertifikat**. Som standard får den namnet REMOTE_Cert_**N** där **n** är ett heltals värde.

### <a name="perform-fortigate-command-line-configuration"></a>Utföra FortiGate kommando rads konfiguration

Följande steg kräver att Azures utloggnings-URL konfigureras. Denna URL innehåller ett frågetecken (?). Särskilda steg krävs för att kunna skicka det här specialtecknet. Det går inte att utföra stegen från FortiGate CLI-konsolen. Upprätta i stället en SSH-session till FortiGate-applicance med ett verktyg som SparaTillFil. Om din FortiGate-installation är en virtuell Azure-dator kan du utföra följande steg från serie konsolen för den virtuella Azure-datorn.

För att utföra de här stegen behöver du de värden som registrerats tidigare:

- Enhets-ID
- Svars-URL
- Utloggnings-URL
- Azure inloggnings-URL
- Azure AD-identifierare
- URL för Azure-utloggning
- Base64 certifikat namn för SAML (REMOTE_Cert_N)

1. Upprätta en SSH-session med din FortiGate-applicance och logga in med ett administratörs konto för FortiGate.
1. Utför följande kommandon:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > **URL:** en för Azure-utloggning innehåller ett- `?` blank steg. Du måste ange en specialorder för att korrekt tillhandahålla URL: en till FortiGate-serie konsolen. URL: en är vanligt vis `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Ange URL: en för Azure-utloggning i serie konsolen genom att ange `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Välj sedan CTRL + V och klistra in resten av webb adressen för att slutföra raden: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

### <a name="configure-fortigate-for-group-matching"></a>Konfigurera FortiGate för grupp matchning

I det här avsnittet ska du konfigurera FortiGate för att identifiera objekt-ID: t för säkerhets gruppen där test användaren finns. Detta gör att FortiGate kan fatta åtkomst beslut baserat på det här grupp medlemskapet.

För att utföra de här stegen behöver du objekt-ID: t för **FortiGateAccess** -säkerhets gruppen som skapades tidigare

1. Upprätta en SSH-session med din FortiGate-Applicance och logga in med ett administratörs konto för FortiGate.
1. Utför följande kommandon:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Skapa FortiGate VPN-portaler och brand Väggs princip

I det här avsnittet konfigurerar du FortiGate VPN-portaler och brand Väggs principer som beviljar åtkomst till säkerhets gruppen **FortiGateAccess** som du skapade ovan.

Arbeta med [Fortigate support-teamet](mailto:tac_amer@fortinet.com) för att lägga till VPN-portaler och brand Väggs principen till Fortigate VPN-plattformen. De här stegen måste utföras innan du använder enkel inloggning.

## <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på FortiGate SSL VPN-panelen på åtkomst panelen, bör du loggas in automatiskt på den FortiGate SSL-VPN som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Microsoft och FortiGate rekommenderar att du använder Fortinet VPN-klienten, FortiClient för den bästa slut användar upplevelsen.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova FortiGate SSL VPN med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
