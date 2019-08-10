---
title: 'Självstudier: Azure Active Directory integration med Check Point CloudGuard Dome9 båge | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Check Point CloudGuard Dome9 båge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 240d962d56e4a2dc0758f3170c51b343d22ef98d
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944574"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Självstudier: Integrera Check Point CloudGuard Dome9 båge med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Check Point CloudGuard Dome9 Arc med Azure Active Directory (Azure AD). När du integrerar Check Point CloudGuard Dome9 båge med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Check Point CloudGuard Dome9 båge.
* Låt användarna vara automatiskt inloggade för att checka in Point CloudGuard Dome9-bågen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Check Point CloudGuard Dome9 Arc Single Sign-on (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Check Point CloudGuard Dome9-båge stöder **SP-och IDP** -initierad SSO.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Lägga till Check Point CloudGuard Dome9 båge från galleriet

Om du vill konfigurera integreringen av Check Point CloudGuard Dome9-bågen i Azure AD måste du lägga till Check Point CloudGuard Dome9 båge från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Check Point CloudGuard Dome9 Arc** i sökrutan.
1. Välj **Check Point CloudGuard Dome9 båge** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med Check Point CloudGuard Dome9 båge med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Check Point CloudGuard Dome9 Arc.

Om du vill konfigurera och testa Azure AD SSO med Check Point CloudGuard Dome9 Arc slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. Konfigurera **[Check Point CloudGuard Dome9 båge](#configure-check-point-cloudguard-dome9-arc)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD](#create-an-azure-ad-test-user)** -testanvändare för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testanvändaren att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa Check Point CloudGuard Dome9 Arc test User](#create-check-point-cloudguard-dome9-arc-test-user)** för att ha en motsvarighet till B. Simon i Check Point CloudGuard Dome9-båge som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Check Point CloudGuard Dome9 Arc** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://secure.dome9.com/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Du väljer värdet för ditt företagsnamn i dome9-administratörsportalen, vilket förklaras senare i den här självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [Check Point CloudGuard Dome9 Arc client support team](mailto:Dome9@checkpoint.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Check Point CloudGuard Dome9 Arc-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

7. Utöver ovan förväntar sig CloudGuard Dome9 ARC-programmet fler attribut att skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn |  Källattribut|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera Check Point CloudGuard Dome9 Arc** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Konfigurera Check Point CloudGuard Dome9 båge

1. Logga in på check punkten CloudGuard Dome9 Arc Company site som administratör i ett annat webbläsarfönster.

2. Klicka på **profilinställningarna** uppe i högra hörnet och klicka sedan på **Account Settings** (Kontoinställningar). 

    ![Kontroll punkt CloudGuard Dome9 Arc Configuration](./media/dome9arc-tutorial/configure1.png)

3. Gå till **SSO** och klicka sedan på **ENABLE**.

    ![Kontroll punkt CloudGuard Dome9 Arc Configuration](./media/dome9arc-tutorial/configure2.png)

4. Utför följande steg i konfigurationsavsnittet för enkel inloggning:

    ![Kontroll punkt CloudGuard Dome9 Arc Configuration](./media/dome9arc-tutorial/configure3.png)

    a. Ange företagets namn i textrutan **Account ID** (Konto-ID). Det här värdet ska användas i svars-URL: en som nämns i avsnittet Azure Portal **grundläggande SAML-konfiguration** .

    b. I text rutan utfärdare klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. I textrutan **Idp endpoint url** (url till Idp-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).

    e. Klicka på **Spara**.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Check Point CloudGuard Dome9 båge.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Check Point CloudGuard Dome9 båge**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Skapa Check Point CloudGuard Dome9 Arc test User

Om du vill att Azure AD-användare ska kunna logga in till Check Point CloudGuard Dome9 båge måste de tillhandahållas i program. Check Point CloudGuard Dome9-bågen stöder just-in-Time-etablering men för att det ska fungera korrekt måste användaren välja en viss **roll** och tilldela den samma för användaren.

   >[!Note]
   >För att skapa **roller** och andra uppgifter kontaktar du [Check Point CloudGuard Dome9 Arc client support team](mailto:Dome9@checkpoint.com).

**Utför följande steg om du vill etablera ett användarkonto manuellt:**

1. Logga in på Check Point CloudGuard Dome9 Arc Company site som administratör.

2. Klicka på **Users & Roles** (Användare och roller) och sedan på **Users** (Användare).

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user1.png)

3. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user2.png)

4. I avsnittet **Skapa användare** utför du följande steg:

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user3.png)

    a. I textrutan **E-post** skriver du e-postadressen för användaren, till exempel B.Simon@contoso.com.

    b. I text rutan Förnamn skriver du förnamn för användaren som B.

    c. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    d. Välj **På** för **användaren**.

    e. Klicka på **SKAPA**.

### <a name="test-sso"></a>Testa SSO

När du väljer ikonen Check Point CloudGuard Dome9 båge på åtkomst panelen, bör du loggas in automatiskt på den Check Point CloudGuard Dome9-båge som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)