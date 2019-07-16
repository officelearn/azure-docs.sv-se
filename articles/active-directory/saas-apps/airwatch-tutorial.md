---
title: 'Självstudier: Azure Active Directory-integrering med AirWatch | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227619"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Självstudier: Integrera AirWatch med Azure Active Directory

I de här självstudierna lär du dig att integrera AirWatch med Azure Active Directory (AD Azure). När du integrerar AirWatch med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till AirWatch.
* Ge dina användare att automatiskt inloggad till AirWatch med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration AirWatch enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för AirWatch **SP** -initierad SSO.

## <a name="adding-airwatch-from-the-gallery"></a>Lägga till AirWatch från galleriet

För att konfigurera integrering av AirWatch i Azure AD behöver du lägga till AirWatch från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **AirWatch** i sökrutan.
1. Välj **AirWatch** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med AirWatch med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i AirWatch för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med AirWatch, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera AirWatch SSO](#configure-airwatch-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa AirWatch-testanvändare](#create-airwatch-test-user)** – för att ha en motsvarighet till Britta Simon i AirWatch som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **AirWatch** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. I textrutan **Identifierare (entitets-ID)** anger du värdet som: `AirWatch`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för AirWatch-klienten](https://www.air-watch.com/company/contact-us/) för att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. AirWatch-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn |  Källattribut|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för Federation** och välj **hämta** att hämta Metadata XML och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **konfigurera AirWatch** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Konfigurera AirWatch SSO

1. I ett annat webbläsarfönster, loggar du in din AirWatch företagets webbplats som administratör.

1. På inställningssidan. Välj **Inställningar > Enterprise-Integration > katalogtjänster**.

   ![Inställningar](./media/airwatch-tutorial/ic791921.png "Inställningar")

1. Klicka på fliken **Användare**. I textrutan **Grundläggande unikt namn** skriver du ditt domännamn och klickar sedan på **Spara**.

   ![Användare](./media/airwatch-tutorial/ic791922.png "Användare")

1. Klicka på fliken **Server**.

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. Utför följande steg på den **LDAP** avsnittet:

    ![Ladda upp](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. För **Katalogtyp** väljer du **Ingen**.

    b. Välj **Use SAML For Authentication** (Använd SAML för autentisering).

1. På den **SAML 2.0** så att ladda upp det nedladdade certifikatet klickar du på **överför**.

    ![Ladda upp](./media/airwatch-tutorial/ic791932.png "Ladda upp")

1. I avsnittet **Begäran** utför du följande steg:

    ![Begäran](./media/airwatch-tutorial/ic791925.png "Begäran")  

    a. För **Request Binding Type** (Begär bindningstyp) väljer du **POST**.

    b. I Azure-portalen på den **Konfigurera enkel inloggning på AirWatch** dialogrutan sida, kopiera den **inloggnings-URL** värdet och klistra in den i den **enkel inloggning på URL för identitetsprovider** textrutan.

    c. För **NameID-format** väljer du **E-postadress**.

    d. Som **begära autentiseringssäkerhet**väljer **ingen**.

    e. Klicka på **Spara**.

1. Klicka på fliken **Användare** igen.

    ![Användare](./media/airwatch-tutorial/ic791926.png "Användare")

1. I avsnittet **Attribut** utför du följande steg:

    ![Attribut](./media/airwatch-tutorial/ic791927.png "Attribut")

    a. I textrutan **Object Identifier** (Objektidentifierare) skriver du `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. I textrutan **Användarnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. I textrutan **Visningsnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. I textrutan **Förnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. I textrutan **Efternamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. I textrutan **E-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AirWatch.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I programlistan väljer du **AirWatch**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-airwatch-test-user"></a>Skapa AirWatch-testanvändare

Om du vill aktivera Azure AD-användare att logga in på AirWatch, måste de vara etablerade i att AirWatch. För AirWatch är etablering en manuell uppgift.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **AirWatch** företagets plats som administratör.

2. I navigeringsfönstret på den vänstra sidan klickar du på **Konton** och sedan på **Användare**.
  
   ![Användare](./media/airwatch-tutorial/ic791929.png "Användare")

3. I den **användare** -menyn klickar du på **listvyn**, och klicka sedan på **Lägg till > Lägg till användare**.
  
   ![Lägg till användare](./media/airwatch-tutorial/ic791930.png "Lägg till användare")

4. I dialogrutan **Add / Edit User** (Lägg till/redigera användare) utför du följande steg:

   ![Lägg till användare](./media/airwatch-tutorial/ic791931.png "Lägg till användare")

   a. Skriv **Användarnamn**, **Lösenord**, **Bekräfta lösenord**, **Förnamn**, **Efternamn** samt **E-postadress** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

   b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa AirWatch-användarkonton som tillhandahålls av AirWatch för att etablera AAD-användarkonton.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen AirWatch i åtkomstpanelen, bör du vara automatiskt inloggad på AirWatch som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
