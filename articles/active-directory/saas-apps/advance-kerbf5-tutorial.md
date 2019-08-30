---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med F5 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e8e6a9eb50d15e6e4eb788fefe13d7e5983a834
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166060"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör det möjligt för användarna att logga in automatiskt på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* F5-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* F5 stöder **SP-och IDP** -INITIERAd SSO
* F5 SSO kan konfigureras på tre olika sätt.

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Lägga till F5 från galleriet

Om du vill konfigurera integrering av F5 i Azure AD måste du lägga till F5 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **F5** i sökrutan.
1. Välj **F5** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurera och testa enkel inloggning med Azure AD för F5

Konfigurera och testa Azure AD SSO med F5 med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i F5.

Om du vill konfigurera och testa Azure AD SSO med F5 slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera F5 – SSO](#configure-f5-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa F5 test användare](#create-f5-test-user)** – om du vill ha en motsvarighet till B. Simon i F5 som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **F5** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<YourCustomFQDN>.f5.com/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<YourCustomFQDN>.f5.com/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [F5-klientens support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

1. I avsnittet **Konfigurera F5** kopierar du lämpliga URL: er baserat på ditt krav.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till F5.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **F5**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-f5-sso"></a>Konfigurera F5 SSO

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Konfigurera enkel inloggning med F5 för avancerat Kerberos-program

1. Öppna ett nytt webbläsarfönster och logga in på din plats med F5 (avancerad Kerberos) som administratör och utför följande steg:

1. Du måste importera certifikatet för metadata till F5 (avancerad Kerberos) som ska användas senare i installations processen. Gå till **System > certifikat hantering > hantering av trafik certifikat > > lista över SSL-certifikat**. Klicka på **Importera** till höger hörn.

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Om du vill konfigurera SAML-IDP går du till **åtkomst > Federation > SAML Service Provider > skapa > från metadata**.

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Ange det certifikat som laddats upp från uppgift 3

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure07.png)

 1. Om du vill konfigurera SAML SP går du till **åtkomst > federation > SAML service Federation > Local SP Services > skapa**.

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klicka på **OK**.

1. Välj SP-konfigurationen och klicka på **BIND/UnBind IDP-kopplingar**.

     ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klicka på **Lägg till ny rad** och välj den **externa IDP-anslutning** som skapades i föregående steg.

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure10.png)
 
1. För att konfigurera Kerberos SSO, **åtkomst > enkel inloggning > Kerberos**

    >[!Note]
    >Du behöver kontot för Kerberos-delegering för att kunna skapas och anges. Se avsnittet KCD (se tillägg för variabel referenser)

    • Användar namn källa`session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Källa för användar sfär`session.logon.last.domain`

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure11.png)

1. För att konfigurera åtkomst profil, **åtkomst > profil/principer > åtkomst profil (per sessionstillstånd)** .

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session. logon. senaste. usernameUPN uttryck {[mcget {session. SAML. Last. Identity}]}

    * session. AD. lastactualdomain TEXT superdemo. Live

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName =% {session. logon. senaste. usernameUPN})

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure19.png)

    * session. logon. senaste. username-uttryck {"[mcget {session. AD. Last. attr. sAMAccountName}]"}

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session. logo. senaste. username}
    * mcget {session. logo. senaste. password}

1. För att lägga till en ny nod går du till **lokal trafik > noder > Node List > +** .

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Om du vill skapa en ny pool går du till **lokal trafik > pooler > pool lista > skapa**.

     ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure22.png)

 1. Om du vill skapa en ny virtuell server går du till **lokal trafik > virtuella servrar > Virtual Server List > +** .

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure23.png)

1. Ange åtkomst profilen som skapades i föregående steg.

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Konfigurera Kerberos-delegering 

>[!Note]
>Mer information finns [här](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Steg 1: Skapa ett Delegerings konto**

    * Exempel på domän namn: superdemo. live Sam-konto namn: Big-ipuser

    * New-ADUser-Name "APM Delegerings konto" host/big-ipuser.superdemo.live@superdemo.live -userPrincipalName-sAMAccountName "Big-ipuser"-PasswordNeverExpires $True-Enabled $True-AccountPassword (Read-Host-AsSecureString "Password! 1234")

* **Steg 2: Ange SPN (på kontot för APM-delegering)**

    *  Exempel setspn – A Host/Big-ipuser. superdemo. live stor – ipuser

* **Steg 3: SPN-delegering (för det App Service kontot)**

    * Konfigurera lämplig delegering för F5-Delegerings kontot.
    * I exemplet nedan konfigureras APM Delegerings kontot för KCD för FRP-app1. superdemo. Live-appen.

        ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure25.png)

1. Ange de uppgifter som anges i referens dokumentet ovan under [detta](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Bilaga – SAML – F5 BIG-IP-variabel mappningar som visas nedan:

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5 (avancerad Kerberos) konfiguration](./media/advance-kerbf5-tutorial/configure27.png) 

1. Nedan visas en fullständig lista över SAML-standardattribut som är standard. GivenName representeras med hjälp av följande sträng.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6. session. SAML. Last. assertionID | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6. session. SAML. Last. assertionIssueInstant  | 2019-06-16T19:18:03.054 Z |
| eb46b6b6. session. SAML. Last. assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/objectidentifier | 90d5f0e5-8f46-4bfd-b40f-ec973d00fcb7 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/tenantid | e6abffcf-4d23-4388-91c2-bfdfcbb1530c |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6. session. SAML. Last. Audience | `https://kerbapp.superdemo.live` |
| eb46b6b6. session. SAML. Last. authNContextClassRef | urn: Oasis: namn: TC: SAML: 2.0: AC: klasser: lösen ord |
| eb46b6b6. session. SAML. Last. authNInstant | 2019-06-16T19:18:00.318 Z |
| eb46b6b6. session. SAML. senaste. identitet | user0@superdemo.live |
| eb46b6b6. session. SAML. Last. inResponseTo | _b9c67faa63a224d7a63f4f3cbb09f78dc05fab |
| eb46b6b6. session. SAML. Last. nameIDValue | user0@superdemo.live |
| eb46b6b6. session. SAML. Last. nameIdFormat | urn: Oasis: Names: TC: SAML: 1.1: NameID-format: emailAddress |
| eb46b6b6. session. SAML. Last. responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. session. SAML. Last. responseId | _a1eca95a-6c41-449e-bb53-1477ba106470 |
| eb46b6b6. session. SAML. Last. responseIssueInstant | 2019-06-16T19:18:03.070 Z |
| eb46b6b6. session. SAML. Last. responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. session. SAML. Last. result | 1 |
| eb46b6b6. session. SAML. Last. samlVersion | 2.0 |
| eb46b6b6. session. SAML. Last. sessionIndex | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6. session. SAML. Last. statusValue | urn: Oasis: namn: TC: SAML: 2.0: status: lyckad |
| eb46b6b6. session. SAML. Last. subjectConfirmDataNotOnOrAfter | 2019-06-16T19:23:03.054 Z |
| eb46b6b6. session. SAML. Last. subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. session. SAML. Last. subjectConfirmMethod | urn: Oasis: Names: TC: SAML: 2.0: cm: Bearer |
| eb46b6b6. session. SAML. Last. validityNotBefore | 2019-06-16T19:13:03.054 Z |
| eb46b6b6. session. SAML. Last. validityNotOnOrAfter | 2019-06-16T20:13:03.054 Z |

### <a name="create-f5-test-user"></a>Skapa F5-test användare

I det här avsnittet skapar du en användare som heter B. Simon i F5. Arbeta med [F5-klient support teamet](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att lägga till användarna på F5-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på F5-panelen i åtkomst panelen, bör du loggas in automatiskt på F5 som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova F5 med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](headerf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

