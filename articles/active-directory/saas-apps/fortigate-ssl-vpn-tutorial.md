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
ms.openlocfilehash: 5b679028a0d99d20497ad1f4e7d870c39b76a136
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658462"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med FortiGate SSL VPN

I den här självstudien får du lära dig hur du integrerar FortiGate SSL VPN med Azure Active Directory (Azure AD). När du integrerar FortiGate SSL VPN med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till FortiGate SSL VPN.
* Gör det möjligt för användarna att logga in automatiskt för att FortiGate SSL VPN med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

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
    1. **[Skapa FORTIGATE SSL VPN test User](#create-fortigate-ssl-vpn-test-user)** – om du vill ha en motsvarighet till B. Simon i FORTIGATE SSL VPN som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

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
    
    | Namn |  Källattribut|
    | ------------ | --------- |
    | användarnamn | user.userprincipalname |
    | grupp | användare. grupper |

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

## <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurera FortiGate SSL VPN SSO

Följ [det här](https://aka.ms/AA9avum) dokumentet om du vill konfigurera enkel inloggning på **Fortigate SSL VPN** -sidan.

> [!NOTE]
> Mer information om hur du konfigurerar FortiGate SSL VPN finns i [den här](https://docs.fortinet.com/document/fortigate/6.4.0/new-features/558169/saml-sp-for-vpn-authentication) länken.

### <a name="create-fortigate-ssl-vpn-test-user"></a>Skapa FortiGate SSL VPN test User

I det här avsnittet skapar du en användare som heter B. Simon i FortiGate SSL VPN. Arbeta med [FORTIGATE SSL VPN support team](mailto:tac_amer@fortinet.com) för att lägga till användarna i Fortigate SSL VPN-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på FortiGate SSL VPN-panelen på åtkomst panelen, bör du loggas in automatiskt på den FortiGate SSL-VPN som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova FortiGate SSL VPN med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
