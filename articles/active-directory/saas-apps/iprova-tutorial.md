---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med iProva | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98458f8be162d0903f5ea0d1f7d4651d46f78e8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048440"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med iProva

I den här självstudien får du lära dig hur du integrerar iProva med Azure Active Directory (Azure AD). När du integrerar iProva med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till iProva.
* Gör att användarna automatiskt loggas in på iProva med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* iProva enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* iProva stöder **SP**-initierad enkel inloggning

* När du har konfigurerat iProva kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Lägga till iProva från galleriet

För att kunna konfigurera integreringen av iProva i Azure AD måste du lägga till iProva från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv iProva i sökrutan i avsnittet Lägg till från **galleriet.** **Add from the gallery**
1. Välj **iProva** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Konfigurera och testa en azure AD-inloggning för iProva

Konfigurera och testa Azure AD SSO med iProva med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i iProva.

Så här konfigurerar och testar du Azure AD SSO med iProva:

1. **[Hämta konfigurationsinformation från](#retrieve-configuration-information-from-iprova)** – som förberedelse för nästa steg.
1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Skapa iProva-testanvändare](#create-iprova-test-user)** – om du vill ha en motsvarighet till B.Simon i iProva som är länkad till Azure AD-representationen av användaren.
1. **[Konfigurera iProva SSO](#configure-iprova-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="retrieve-configuration-information-from-iprova"></a>Hämta konfigurationsinformation från iProva

I det här avsnittet hämtar du information från iProva för att konfigurera enkel inloggning i Azure AD.

1. Öppna en webbläsare och gå till sidan **SAML2-info** i iProva med hjälp av följande URL-mönster:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Visa SAML2-informationssidan i iProva](media/iprova-tutorial/iprova-saml2-info.png)

1. Låt webbläsarfliken vara öppen medan du fortsätter med nästa steg i en annan webbläsarflik.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **iProva** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Fyll i rutan **Inloggnings-URL** med det värde som visas bakom etiketten **Inloggnings-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    b. Fyll i rutan **Identifierare** med det värde som visas bakom etiketten **EntityID** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    c. Fyll i rutan **Svars-URL** med det värde som visas bakom etiketten **Svars-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

1. iProva-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan, iProva ansökan förväntar sig några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut| Namnområde  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

## <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iProva.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **iProva** i programlistan.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="create-iprova-test-user"></a>Skapa iProva-testanvändare

1. Logga in på iProva med hjälp av **administratörskontot**.

2. Öppna menyn **Gå till**.

3. Välj **Programhantering**.

4. Välj **Användare** i panelen **Användare och användargrupper**.

5. Välj **Lägg till**.

6. I rutan **Användarnamn** anger du användarnamnet för användaren som `B.Simon@contoso.com`.

7. Ange ett fullständigt namn på användaren som **B.Simon**i rutan **Fullständigt namn** .

8. Välj alternativet **Inget lösenord (använd enkel inloggning)**.

9. I rutan **E-postadress** anger du e-postadressen för användare som `B.Simon@contoso.com`.

10. Rulla ned till slutet av sidan och välj **Slutför**.

## <a name="configure-iprova-sso"></a>Konfigurera iProva SSO

1. Logga in på iProva med hjälp av **administratörskontot**.

2. Öppna menyn **Gå till**.

3. Välj **Programhantering**.

4. Välj **Allmänt** i panelen **Systeminställningar**.

5. Välj **Redigera**.

6. Rulla ned till **Åtkomstkontroll**.

    ![Inställningar för åtkomstkontroll i iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Hitta inställningen **Users are automatically logged on with their network accounts** (Användare loggas automatiskt in med nätverkskonton) och ändra det till **Yes, authentication via SAML** (Ja, autentisering visa SAML). Ytterligare alternativ visas nu.

8. Välj **Konfigurera**.

9. Välj **Nästa**.

10. iProva frågar dig om du vill ladda ned federationsdata från en URL eller ladda upp dem från en fil. Välj alternativet **Från URL**.

    ![Ladda ned Azure AD-metadata](media/iprova-tutorial/iprova-download-metadata.png)

11. Klistra in den metadata-URL som du sparade i det sista steget i kapitlet Konfigurera enkel inloggning med Azure AD.

12. Välj den pilformade knappen för att ladda ned metadata från Azure AD.

13. När nedladdningen är slutförd visas bekräftelsemeddelandet om **Valid Federation Data file downloaded** (Giltig federationsdatafil har laddats ned).

14. Välj **Nästa**.

15. Hoppa över alternativet **Testa inloggning** för tillfället och välj **Nästa**.

16. I listrutan **Claim to use** (Anspråk att använda) väljer du **windowsaccountname**.

17. Välj **Slutför**.

18. Nu kan du gå tillbaka till sidan **Redigera allmänna inställningar**. Rulla ned till slutet av sidan och välj **OK** för att spara konfigurationen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på iProva-panelen i åtkomstpanelen så bör du automatiskt loggas in på den iProva som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova iProva med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du iProva med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)