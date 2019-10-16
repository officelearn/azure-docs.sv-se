---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med PureCloud av gener | Microsoft Docs'
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
ms.openlocfilehash: dfaa5a4ebb8bc633dc49db08698aec31de9436d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373123"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med PureCloud av gener

I den här självstudien får du lära dig hur du integrerar PureCloud av gener med Azure Active Directory (Azure AD). När du integrerar PureCloud av gener med Azure AD kan du:

* Kontroll i Azure AD som har till gång till PureCloud av gener.
* Gör det möjligt för användarna att logga in automatiskt till PureCloud av gener med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* PureCloud av generad enkel inloggning (SSO) med en aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* PureCloud by Genesys har stöd för **SP- och IDP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Lägga till PureCloud by Genesys från galleriet

För att konfigurera integreringen av PureCloud by Genesys i Azure AD måste du lägga till PureCloud by Genesys från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **PureCloud av gener** i sökrutan.
1. Välj **PureCloud av gener** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Konfigurera och testa enkel inloggning med Azure AD för PureCloud av gener

Konfigurera och testa Azure AD SSO med PureCloud genom gener som använder en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i PureCloud av gener.

Om du vill konfigurera och testa Azure AD SSO med PureCloud av gener, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PureCloud genom gener SSO](#configure-purecloud-by-genesys-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa PureCloud av geners test User](#create-purecloud-by-genesys-test-user)** -om du vill ha en motsvarighet till B. Simon i PureCloud av gener som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **PureCloud av generad** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL enligt din region:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. I textrutan **Svars-URL** skriver du en URL enligt din region:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du en URL enligt din region:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. PureCloud by Genesys-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![mallar](common/default-attributes.png)

1. Utöver ovan förväntar sig PureCloud av Generning-appen några fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | E-post | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera PureCloud av gener** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PureCloud av gener.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **PureCloud by Genesys**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurera PureCloud av geners SSO

1. Logga in på PureCloud från gener som administratör i ett annat webbläsarfönster.

1. Klicka på **Administratör** längst upp och gå till **Enkel inloggning** under **Integrationer**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Växla till fliken **ADFS/Azure AD(Premium)** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Klicka på **Bläddra** för att ladda upp det base-64-kodade certifikat som du har laddat ned från Azure-portalen till **ADFS-certifikatet**.

    b. I textrutan för **URI för ADFS-utfärdare** klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I textrutan **Mål-URI** klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    d. För värdet **Identifierare för förlitande part** behöver du gå till Azure-portalen, programintegreringssidan för **PureCloud by Genesys**, klicka på fliken **Egenskaper** och kopiera värdet **Program-ID**. Klistra in det i textrutan **Identifierare för förlitande part**. 

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Klicka på **Spara**

### <a name="create-purecloud-by-genesys-test-user"></a>Skapa PureCloud by Genesys-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på PureCloud av gener, måste de tillhandahållas i PureCloud av gener. I PureCloud by Genesys är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in i PureCloud by Genesys som administratör.

1. Klicka på **Administratör** längst upp och gå till **Personer** under **Personer och behörigheter**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure03.png)

1. På sidan Personer klickar du på **Lägg till person**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure04.png)

1. I popup-fönstret **Lägg till personer i organisationen** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. I text rutan **fullständigt namn** anger du namnet på användaren, t **. ex. B. Simon**.

    b. I text rutan **e-postadress** anger du e-postadressen till användaren som **b.Simon\@contoso.com**.

    c. Klicka på **Skapa**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på PureCloud by Genesys-panelen i åtkomstpanelen bör du automatiskt loggas in på PureCloud by Genesys som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova PureCloud av gener med Azure AD](https://aad.portal.azure.com/)