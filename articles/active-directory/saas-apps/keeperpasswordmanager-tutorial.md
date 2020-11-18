---
title: 'Självstudie: Azure Active Directory integration med lösen ords hanteraren i Keepr & digitalt valv | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Keeper Password Manager & Digital Vault.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 88f84fba43959ee5e5b8d93446e4985a75697813
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685876"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Självstudie: Azure Active Directory integrering med lösen ords hanteraren i Keepr & digitalt valv

I den här självstudien lär du dig att integrera Keeper Password Manager & Digital Vault med Azure Active Directory (AD Azure).
Genom att integrera Keeper Password Manager & Digital Vault med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Keeper Password Manager & Digital Vault.
* Du kan göra så att dina användare automatiskt loggas in på Keeper Password Manager & Digital Vault (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.


## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Keeper Password Manager & Digital Vault behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Keeper Password Manager & Digital Vault-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Keeper Password Manager & Digital Vault stöder **IDP**-initierad enkel inloggning

* Keeper Password Manager & Digital Vault stöder **just in time**-användaretablering

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Lägga till Keeper Password Manager & Digital Vault från galleriet

För att konfigurera integreringen av Keeper Password Manager & Digital Vault till Azure AD behöver du lägga till Keeper Password Manager & Digital Vault från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **behållare lösen ords hanteraren & digitala valvet** i sökrutan.
1. Välj **behållar lösen ords hanteraren & digitalt valv** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Konfigurera och testa Azure AD SSO för lösen ords hanteraren i & Digital Vault

Konfigurera och testa Azure AD SSO med lösen ords hanteraren & digitalt valv med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i lösen ords hanteraren för lösen ord & digitalt valv.

Om du vill konfigurera och testa Azure AD SSO med lösen ords hanteraren & digitalt valv slutför du följande Bygg stenar::

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.

    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.

1. **[Konfigurera lösen ords hanteraren i & Digital Vault SSO](#configure-keeper-password-manager--digital-vault-sso)** – för att konfigurera de enskilda Sign-On inställningarna på program sidan.
    * **[Skapa Keeper Password Manager & Digital Vault-testanvändare](#create-keeper-password-manager--digital-vault-test-user)** – för att ha en motsvarighet för Britta Simon i Keeper Password Manager & Digital Vault som är länkad till en Azure AD-representation av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **lösen ords hanteraren för lösen ords hanterare & Digital Vault** program integration, letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 
    * För **enkel inloggning i molnet** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * För **lokal SSO** : `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
    * För **enkel inloggning i molnet** : `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * För **lokal SSO** : `https://<KEEPER_FQDN>/sso-connect`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 
    * För **enkel inloggning i molnet** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * För **lokal SSO** : `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för Keeper Password Manager & Digital Vault-klienten](https://keepersecurity.com/contact.html). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Behållar lösen ords hanteraren & Digital Vault applikation förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig lösen ords hanteraren & digitala valv programmet förväntar sig fler attribut som ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ------------| --------- |
    | Första | user.givenname |
    | Sista | user.surname |
    | E-post | user.mail |

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Keeper Password Manager & Digital Vault** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Exempelvis `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till lösen ords hanteraren för & digitalt valv.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Keeper Password Manager & Digital Vault**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Konfigurera lösen ords hanteraren i & Digital Vault SSO

Konfigurera enkel inloggning på sidan om **Keeper Password Manager & Digital Vault-konfiguration** genom att följa anvisningarna i [supportguiden för Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Skapa Keeper Password Manager & Digital Vault-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Keeper Password Manager & Digital Vault måste de etableras i Keeper Password Manager & Digital Vault. Programmet stöder just-in-time-användaretablering, och efter autentiseringen skapas användare automatiskt i programmet. Du kan kontakta [Keeper-supporten](https://keepersecurity.com/contact.html) om du vill konfigurera användare manuellt.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Keepr Password Manager & Digital Vault inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till behållar lösen ords hanteraren & Digital Vault inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Access-panelen. När du klickar på panelen för lösen ords hanteraren & Digital Vault på åtkomst panelen, omdirigeras det till behållar lösen ords hanteraren & Digital Vault inloggnings-URL. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat lösen ords hanteraren & digitala valvet kan du framtvinga kontroll av sessionen, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)