---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Nuclino | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nuclino.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 4307838df56fb2dc1cdf6ee4044b8ce8de164227
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518610"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Nuclino

I den här självstudien får du lära dig hur du integrerar Nuclino med Azure Active Directory (Azure AD). När du integrerar Nuclino med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Nuclino.
* Gör det möjligt för användarna att logga in automatiskt till Nuclino med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Nuclino för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Nuclino stöder **SP-och IDP** -INITIERAd SSO
* Nuclino stöder **just-in-Time** User-etablering

## <a name="adding-nuclino-from-the-gallery"></a>Lägga till Nuclino från galleriet

Om du vill konfigurera integreringen av Nuclino i Azure AD måste du lägga till Nuclino från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Nuclino** i sökrutan.
1. Välj **Nuclino** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Konfigurera och testa enkel inloggning med Azure AD för Nuclino

Konfigurera och testa Azure AD SSO med Nuclino med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Nuclino.

Om du vill konfigurera och testa Azure AD SSO med Nuclino, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera NUCLINO SSO](#configure-nuclino-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Nuclino test User](#create-nuclino-test-user)** -om du vill ha en motsvarighet till B. Simon i Nuclino som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Nuclino** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren och svars-URL: en från avsnittet **Authentication** , som beskrivs senare i den här självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Nuclino client support team](mailto:contact@nuclino.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Nuclino-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

7. Utöver ovan förväntar sig Nuclino-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Nuclino** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Nuclino.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Nuclino**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-nuclino-sso"></a>Konfigurera Nuclino SSO

1. Om du vill automatisera konfigurationen i Nuclino måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera Nuclino** för att dirigera dig till Nuclino-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Nuclino. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Nuclino manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Nuclino-företags webbplats som administratör och utför följande steg:

4. Klicka på **ikonen**.

    ![Skärm bild som visar ikonen "meny" som marker ATS bredvid "Azure A D S O".](./media/nuclino-tutorial/configure1.png)

5. Klicka på **Azure AD SSO** och välj **grupp inställningar** i list rutan.

    ![Skärm bild som visar List rutan "Azure A D S S O" och "team Settings" markerat.](./media/nuclino-tutorial/configure2.png)

6. Välj **autentisering** från vänster navigerings fönster.

    ![Skärm bild som visar "autentisering" vald.](./media/nuclino-tutorial/configure3.png)

7. I avsnittet **autentisering** , utför följande steg:

    ![Nuclino-konfiguration](./media/nuclino-tutorial/configure4.png)

    a. Välj **SAML-baserad enkel inloggning (SSO)**.

    b. Kopiera **ACS-URL (du måste kopiera och klistra in det i SSO-providern)** och klistra in det i text rutan **SVARs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    c. Kopiera **entitets-ID (du måste kopiera och klistra in det i SSO-providern)** och klistra in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    d. I text rutan **SSO-URL** klistrar du in värdet för **inloggnings-URL:** en som du har kopierat från Azure Portal.

    e. I text rutan **entitets-ID** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.

    f. Öppna den nedladdade **Certificate(Base64)**-filen i Anteckningar. Kopiera innehållet i den till Urklipp och klistra in det i text rutan **offentligt certifikat** .

    ex. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

### <a name="create-nuclino-test-user"></a>Skapa Nuclino test användare

I det här avsnittet skapas en användare som heter B. Simon i Nuclino. Nuclino stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Nuclino skapas en ny efter autentiseringen.

> [!Note]
> Kontakta [Nuclino support team](mailto:contact@nuclino.com)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Nuclino på åtkomst panelen, bör du loggas in automatiskt på den Nuclino som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova Nuclino med Azure AD](https://aad.portal.azure.com/)