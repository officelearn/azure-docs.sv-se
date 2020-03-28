---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med PureCloud av Genesys | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dac8e0f2e10906f2cc56ecf86e0cc70947cb7e85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897769"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med PureCloud by Genesys

I den här självstudien får du lära dig hur du integrerar PureCloud by Genesys med Azure Active Directory (Azure AD). När du har gjort det kan du:

* Använd Azure AD för att styra vilka användare som kan komma åt PureCloud by Genesys.
* Gör det möjligt för användarna att automatiskt loggas in på PureCloud av Genesys med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en, kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En PureCloud från Genesys single sign-on (SSO)–aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* PureCloud by Genesys stöder **SP och IDP**–initierad SSO.

> [!NOTE]
> Eftersom ID:t för det här programmet är ett värde med fast sträng kan endast en instans konfigureras i en klient.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Lägga till PureCloud by Genesys från galleriet

Om du vill konfigurera integreringen av PureCloud by Genesys i Azure AD måste du lägga till PureCloud by Genesys från galleriet i listan över hanterade SaaS-appar. Det gör du genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av ett arbets- eller skolkonto eller med hjälp av ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **PureCloud by Genesys** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **PureCloud av Genesys** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Konfigurera och testa Azure AD enkel inloggning för PureCloud by Genesys

Konfigurera och testa Azure AD SSO med PureCloud by Genesys med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i PureCloud by Genesys.

Om du vill konfigurera och testa Azure AD SSO med PureCloud by Genesys slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. **[Konfigurera PureCloud av Genesys SSO](#configure-purecloud-by-genesys-sso)** för att konfigurera de enda inloggningsinställningarna på applikationssidan.
    1. **[Skapa en PureCloud av Genesys testanvändare](#create-purecloud-by-genesys-test-user)** för att ha en motsvarighet till B.Simon i PureCloud av Genesys som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Så här aktiverar du Azure AD SSO i Azure-portalen:

1. I [Azure-portalen](https://portal.azure.com/)på sidan **PureCloud by Genesys-programintegrering** hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en metod** för enkel inloggning väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat**läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Ange en URL som motsvarar din region i rutan **Identifierare:**

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Ange en URL som motsvarar din region i rutan **Svars-URL:**

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Välj **Ange ytterligare webbadresser** och ta följande steg om du vill konfigurera programmet i **sp-initierat** läge:

    Ange en URL som motsvarar din region i rutan **Inloggnings-URL:**
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. PureCloud by Genesys-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar listan över standardattribut:

    ![image](common/default-attributes.png)

1. Dessutom förväntar purecloud by Genesys ansökan några fler attribut som ska skickas tillbaka i SAML-svaret, som visas i följande tabell. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | E-post | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera PureCloud by Genesys kopierar** du lämplig webbadress (eller webbadresser) baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet B.Simon i Azure-portalen:

1. I den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**, väljer **Användare**och väljer sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange användarnamnet i följande format i fältet username@companydomain.extension **Användarnamn:** . Till exempel: `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och notera sedan värdet som visas i rutan **Lösenord.**
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet konfigurerar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PureCloud by Genesys.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **PureCloud by Genesys**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och väljer sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och väljer sedan knappen **Välj** längst ned på skärmen.
1. Välj knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurera PureCloud av Genesys SSO

1. I ett annat webbläsarfönster loggar du in på PureCloud by Genesys som administratör.

1. Välj **Admin** högst upp och gå sedan till **Enkel inloggning** under **Integrationer**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Växla till fliken **ADFS/Azure AD(Premium)** och gör så här:

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Välj **Bläddra om** du vill ladda upp det kodade bas-64-certifikatet som du hämtade från Azure-portalen till **ADFS-certifikatet**.

    b. Klistra in värdet för **Azure AD-identifierare** som du kopierade från Azure-portalen i **rutan ADFS Issuer.**

    c. I rutan **Mål-URI** klistrar du in värdet **för inloggnings-URL:en** som du kopierade från Azure-portalen.

    d. För värdet **För förlitande part-identifierare** går du till Azure-portalen och väljer sedan fliken Egenskaper på sidan **PureCloud by Genesys-programintegration** och **kopierar värdet Program-ID.** **Properties** Klistra in den i rutan **Identifierar för förlitande part.**

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Välj **Spara**.

### <a name="create-purecloud-by-genesys-test-user"></a>Skapa PureCloud by Genesys-testanvändare

För att Azure AD-användare ska kunna logga in på PureCloud by Genesys måste de etableras i PureCloud av Genesys. I PureCloud by Genesys är etablering en manuell uppgift.

**Så här etablerar du ett användarkonto:**

1. Logga in på PureCloud av Genesys som administratör.

1. Välj **Admin** högst upp och gå till **Personer** under Personer **& behörigheter**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure03.png)

1. På sidan **Kontakter** väljer du **Lägg till person**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Gör så här i dialogrutan **Lägg till personer i organisationen:**

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Ange namnet på en användare i rutan **Fullständigt namn.** Till exempel: **B.simon**.

    b. Ange användarens e-post i rutan **E-post.** Till exempel: **b.simon\@contoso.com**.

    c. Välj **Skapa**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer panelen **PureCloud by Genesys** i åtkomstpanelen ska du automatiskt loggas in på purecloud-kontot av Genesys som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova PureCloud från Genesys med Azure AD](https://aad.portal.azure.com/)
