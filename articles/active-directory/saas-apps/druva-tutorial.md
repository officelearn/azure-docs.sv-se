---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Druva | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f019d818fb5a017d184bda8d773eb0aaf0f3645a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Druva

I den här självstudien får du lära dig hur du integrerar Druva med Azure Active Directory (Azure AD). När du integrerar Druva med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Druva.
* Gör att användarna automatiskt loggas in på Druva med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Druva enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Druva stöder **IDP** initierad SSO
* När du har konfigurerat Druva SSO kan du framtvinga sessionskontroll, vilket skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-druva-from-the-gallery"></a>Lägga till Druva från galleriet

För att konfigurera integrering av Druva i Azure AD behöver du lägga till Druva från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Druva** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Druva** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Konfigurera och testa en azure AD-inloggning för Druva

Konfigurera och testa Azure AD SSO med Druva med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Druva.

Om du vill konfigurera och testa Azure AD SSO med Druva slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Druva SSO](#configure-druva-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Druva-testanvändare](#create-druva-test-user)** - om du vill ha en motsvarighet till B.Simon i Druva som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Druva**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Skriv strängvärdet i textrutan **Identifierare (entitets-ID):** `DCP-login`.
    
    b. Skriv URL:en i textrutan **Svarsadress (Kontroll konsumenttjänsts URL)** `https://cloud.druva.com/wrsaml/consume`.

1. Klicka på **Spara**.

1. Druva-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar Druva ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ------------------- | -------------------- |
    | Emailaddress | användare.e-post |
    | druva_auth_token | SSO-token som genereras från DCP Admin Console, utan citattecken.  Till exempel: X-XXXXX-XXXX-S-A-M-P-L-E+TXOXKXEXNX=. Azure lägger automatiskt till citattecken runt auth-token. |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Druva.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Druva.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Druva**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-druva-sso"></a>Konfigurera Druva SSO

1. I ett annat webbläsarfönster loggar du in på din Druva-företagswebbplats som administratör.

1. Klicka på Druva-logotypen längst upp till vänster och klicka sedan på **Druva Cloud Settings**.

    ![Inställningar](./media/druva-tutorial/ic795091.png "Inställningar")

1. Klicka på **Redigera**på fliken **Enkel inloggning.**

    ![Inställningar för enkel inloggning](./media/druva-tutorial/ic795092.png "Inställningar för enkel inloggning")

1. Gör följande på sidan **Redigera inställningar för enkel inloggning:**

    ![Inställningar för enkel inloggning](./media/druva-tutorial/ic795095.png "Inställningar för enkel inloggning")

    1. I textrutan **ID Provider Login URL** (Inloggnings-URL för ID-provider) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    1. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **ID Provider Certificate** (Certifikat för ID-provider)

       > [!NOTE]
       > Om du vill aktivera enkel inloggning för administratörer väljer du **Administratörer logga in på Druva Cloud via SSO-providern** och tillåt **felsäker åtkomst till Druva Cloud-administratörer(rekommenderas)** kryssrutor. Druva rekommenderar att du aktiverar **Felsäkert för administratörer** så att de måste komma åt DCP-konsolen vid fel i IdP. Det gör det också möjligt för administratörerna att använda både SSO och DCP lösenord för att komma åt DCP-konsolen.

    1. Klicka på **Spara**. Detta ger åtkomst till Druva Cloud Platform med SSO.

### <a name="create-druva-test-user"></a>Skapa Druva-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Druva. Druva stöder just-in-time användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Druva skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Druva-panelen i åtkomstpanelen bör du automatiskt loggas in på Druva som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Druva med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)