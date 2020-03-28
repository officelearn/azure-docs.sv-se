---
title: 'Självstudiekurs: Azure Active Directory-integrering med AirWatch | Microsoft-dokument'
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
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231996"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Självstudiekurs: Integrera AirWatch med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar AirWatch med Azure Active Directory (Azure AD). När du integrerar AirWatch med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AirWatch.
* Gör att användarna automatiskt loggas in på AirWatch med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* AirWatch enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. AirWatch stöder **SP** initierade SSO.

## <a name="adding-airwatch-from-the-gallery"></a>Lägga till AirWatch från galleriet

För att konfigurera integrering av AirWatch i Azure AD behöver du lägga till AirWatch från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **AirWatch** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **AirWatch** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med AirWatch med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i AirWatch.

Om du vill konfigurera och testa Azure AD SSO med AirWatch slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera AirWatch SSO](#configure-airwatch-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa AirWatch-testanvändare](#create-airwatch-test-user)** – för att ha en motsvarighet till Britta Simon i AirWatch som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

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

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta metadata-XML:n och spara den på datorn.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera AirWatch** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Konfigurera AirWatch SSO

1. Logga in på airwatch-företagets webbplats som administratör i ett annat webbläsarfönster.

1. På inställningssidan. Välj Inställningar > > katalogtjänster för **företagsintegrering**.

   ![Inställningar](./media/airwatch-tutorial/ic791921.png "Inställningar")

1. Klicka på fliken **Användare**. I textrutan **Grundläggande unikt namn** skriver du ditt domännamn och klickar sedan på **Spara**.

   ![Användare](./media/airwatch-tutorial/ic791922.png "Användare")

1. Klicka på fliken **Server**.

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. Utför följande steg i **avsnittet LDAP:**

    ![Överför](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. För **Katalogtyp** väljer du **Ingen**.

    b. Välj **Use SAML For Authentication** (Använd SAML för autentisering).

1. Klicka på **Ladda**upp om du vill ladda upp det nedladdade certifikatet i avsnittet **SAML 2.0.**

    ![Överför](./media/airwatch-tutorial/ic791932.png "Ladda upp")

1. I avsnittet **Begäran** utför du följande steg:

    ![Förfrågan](./media/airwatch-tutorial/ic791925.png "Förfrågan")  

    a. För **Request Binding Type** (Begär bindningstyp) väljer du **POST**.

    b. På dialogsidan Konfigurera **enkel inloggning på AirWatch** i Azure-portalen kopierar du värdet **för inloggnings-URL** och klistrar sedan in det i textrutan **för en inloggning på identitetsprovidern.**

    c. För **NameID-format** väljer du **E-postadress**.

    d. Som **säkerhet för autentiseringsbegäran**väljer du **Ingen**.

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

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AirWatch.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **AirWatch**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-airwatch-test-user"></a>Skapa AirWatch-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på AirWatch måste de etableras i AirWatch. För AirWatch är etablering en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på **airwatch-företagets** webbplats som administratör.

2. I navigeringsfönstret på den vänstra sidan klickar du på **Konton** och sedan på **Användare**.
  
   ![Användare](./media/airwatch-tutorial/ic791929.png "Användare")

3. Klicka på **Listvy**på **Menyn Användare** och klicka sedan på Lägg till > Lägg **till användare**.
  
   ![Lägg till användare](./media/airwatch-tutorial/ic791930.png "Lägg till användare")

4. I dialogrutan **Add / Edit User** (Lägg till/redigera användare) utför du följande steg:

   ![Lägg till användare](./media/airwatch-tutorial/ic791931.png "Lägg till användare")

   a. Skriv **Användarnamn**, **Lösenord**, **Bekräfta lösenord**, **Förnamn**, **Efternamn** samt **E-postadress** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

   b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra Verktyg eller API:er för att skapa airwatch-användarkonton för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

När du väljer Panelen AirWatch på åtkomstpanelen ska du automatiskt loggas in på den AirWatch som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
