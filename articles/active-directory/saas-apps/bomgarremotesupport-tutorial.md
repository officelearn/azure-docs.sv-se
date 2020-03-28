---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med BeyondTrust Remote Support | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BeyondTrust Remote Support.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74082077"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med BeyondTrust-fjärrsupport

I den här självstudien får du lära dig hur du integrerar BeyondTrust Remote Support med Azure Active Directory (Azure AD). När du integrerar BeyondTrust Remote Support med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BeyondTrust Remote Support.
* Gör att användarna automatiskt loggas in på BeyondTrust Remote Support med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* BeyondTrust Remote Support enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* BeyondTrust Remote Support stöder **SP** initierad SSO
* BeyondTrust Remote Support stöder **just in time-användares** etablering

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Lägga till BeyondTrust-fjärrsupport från galleriet

Om du vill konfigurera integreringen av BeyondTrust Remote Support i Azure AD måste du lägga till BeyondTrust-fjärrsupport från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **BeyondTrust-fjärrstöd** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **BeyondTrust Fjärrsupport** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Konfigurera och testa azure AD-enkel inloggning för BeyondTrust-fjärrsupport

Konfigurera och testa Azure AD SSO med BeyondTrust Remote Support med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i BeyondTrust Remote Support.

Om du vill konfigurera och testa Azure AD SSO med BeyondTrust Remote Support slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera BeyondTrust Remote Support SSO](#configure-beyondtrust-remote-support-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa BeyondTrust Remote Support-testanvändare](#create-beyondtrust-remote-support-test-user)** – om du vill ha en motsvarighet till B.Simon i BeyondTrust Remote Support som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Programintegrering av** **Fjärrstöd** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<HOSTNAME>.bomgar.com/saml`

    b. Skriv en URL med följande mönster i rutan **Identifierare:**`https://<HOSTNAME>.bomgar.com`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Du kommer att få dessa värden förklaras senare i handledningen.

1. BeyondTrust Remote Support-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig BeyondTrust Remote Support-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | ---------------| ----------|
    | Givennamn | user.givenname |
    | Emailaddress | user.mail |
    | Namn | user.userprincipalname |
    | Användarnamn | user.userprincipalname |
    | Grupper | användare.grupper |
    | Unik användaridentifierare | user.userprincipalname |

    > [!NOTE]
    > När du tilldelar Azure AD-grupper för BeyondTrust Remote Support-programmet måste alternativet Grupper som returneras i anspråk ändras från Ingen till SecurityGroup. Grupperna importeras till programmet som objekt-ID. Objekt-ID för Azure AD-gruppen kan hittas genom att kontrollera egenskaperna i Azure Active Directory-gränssnittet. Detta krävs för att referera och tilldela Azure AD-grupper till rätt gruppprinciper.

1. När du anger den unika användaridentifieraren måste det här värdet anges till NameID-Format: **Persistent**. Vi kräver att detta är en beständig identifierare för att korrekt identifiera och associera användaren till rätt gruppprinciper för behörigheter. Klicka på redigeringsikonen för att öppna dialogrutan **Användarattribut & anspråk** för att redigera värdet Unik användaridentifierare.

1. Klicka på **formatet Välj namnidentifierare** i avsnittet **Hantera anspråk** och ange värdet till **Beständigt** och klicka på **Spara**.

    ![Användarattribut och anspråk](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera BeyondTrust-fjärrsupport.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BeyondTrust Remote Support.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **BeyondTrust Remote Support**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-beyondtrust-remote-support-sso"></a>Konfigurera BeyondTrust Remote Support SSO

1. I ett annat webbläsarfönster loggar du in på BeyondTrust Remote Support som administratör.

1. Klicka på **STATUS-menyn** och kopiera **url:en identifierare,** **svars-URL** och **Logga in på URL** och använd dessa värden i avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen.

    ![Konfigurera BeyondTrust-fjärrsupport](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Navigera till BeyondTrust Remote Support `https://support.example.com/login` /login-gränssnittet där **support.example.com** är den primära värdnamnet på din apparat och autentisera med dina administrativa autentiseringsuppgifter.

1. Navigera till användare & > **säkerhetssäkerhetsleverantörer**. **Users & Security**

1. Välj **SAML** på den nedrullningsbara menyn och klicka på knappen **Skapa provider.**

1. Under avsnittet Inställningar för identitetsprovider finns det ett alternativ för att ladda upp identitetsproviderns metadata. Leta reda på xml-filen metadata som du har hämtat från Azure-portalen och klicka på **knappen Ladda upp.** **Enhets-ID,** **URL för enkel inloggningstjänst** och certifikat laddas upp automatiskt och **protokollbindningen** måste ändras till **HTTP POST**. Se skärmdump nedan:

    ![Konfigurera BeyondTrust-fjärrsupport](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Skapa BeyondTrust Remote Support-testanvändare

Vi kommer att konfigurera inställningarna för användaretablering här. De värden som används i det här avsnittet refereras från avsnittet **Användarattribut & anspråk** i Azure-portalen. Vi har konfigurerat detta till standardvärden som redan importeras när det skapas, men värdet kan anpassas om det behövs.

![Skapa användare](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Attributet grupper och e-post är inte nödvändiga för den här implementeringen. Om du använder Azure AD-grupper och tilldelar dem till BeyondTrust Remote Support Group Policies för behörigheter, måste gruppens objekt-ID refereras via dess egenskaper i Azure-portalen och placeras i avsnittet Tillgängliga grupper. När detta har slutförts är object ID/AD-gruppen nu tillgänglig för tilldelning till en grupprincip för behörigheter.

![Skapa användare](./media/bomgarremotesupport-tutorial/config-user2.png)

![Skapa användare](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Alternativt kan en standardgruppprincip ställas in på SAML2-säkerhetsprovidern. Genom att definiera det här alternativet tilldelar detta alla användare som autentiserar genom SAML de behörigheter som anges i grupprincipen. Principen för allmänna medlemmar ingår i BeyondTrust Remote Support/Privileged Remote Access med begränsad behörighet, som kan användas för att testa autentisering och tilldela användare rätt principer. Användare kommer inte att fylla i SAML2-listan användare via /login > Användare & säkerhet förrän det första lyckade autentiseringsförsöket. Ytterligare information om grupprinciper finns på följande länk:`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen BeyondTrust-fjärrstöd på åtkomstpanelen bör du automatiskt loggas in på det BeyondTrust-fjärrsupport som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova BeyondTrust Remote Support med Azure AD](https://aad.portal.azure.com/)
