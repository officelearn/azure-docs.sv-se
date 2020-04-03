---
title: 'Självstudiekurs: Azure Active Directory-integrering med Zendesk | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70e7067a78b439d3dcaf0b83460296cad7b4485
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585810"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Zendesk

I den här självstudien får du lära dig hur du integrerar Zendesk med Azure Active Directory (Azure AD). När du integrerar Zendesk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zendesk.
* Gör att användarna automatiskt loggas in på Zendesk med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Zendesk enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Zendesk stöder **SP**-initierad enkel inloggning
* Zendesk har stöd för [**automatisk** användaretablering](zendesk-provisioning-tutorial.md)
* När du har konfigurerat Zendesk kan du framtvinga sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>Lägga till Zendesk från galleriet

För att konfigurera integrering av Zendesk i Azure AD måste du lägga till Zendesk från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Zendesk** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Zendesk** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Konfigurera och testa en azure AD-inloggning för Zendesk

Konfigurera och testa Azure AD SSO med Zendesk med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zendesk.

Om du vill konfigurera och testa Azure AD SSO med Zendesk slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Zendesk SSO](#configure-zendesk-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Zendesk-testanvändare](#create-zendesk-test-user)** – om du vill ha en motsvarighet till B.Simon i Zendesk som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.zendesk.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.zendesk.com`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Kontakta [Zendesk-klients supportteam](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Zendesk-program förväntar SAML-försäkran i ett visst format. Det finns inga obligatoriska SAML-attribut, men du kan också hantera från avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Du kan använda tilläggsattribut för att lägga till attribut som inte ingår i Azure AD som standard. Klicka på [Användarattribut som kan konfigureras i SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) för att hämta den fullständiga listan med SAML-attribut som **Zendesk** accepterar.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **tumavtrycksvärdet** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Zendesk** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zendesk.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Zendesk**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-zendesk-sso"></a>Konfigurera Zendesk SSO

1. Om du vill konfigurera Zendesk manuellt öppnar du ett nytt webbläsarfönster och loggar in på zendesk-företagets webbplats som administratör och utför följande steg:

2. Klicka på **Admin**.

3. I det vänstra navigeringsfönstret klickar du på **Inställningar** och sedan på **Säkerhet**.

4. På sidan **Säkerhet** utför du följande steg:

    ![Säkerhet](./media/zendesk-tutorial/ic773089.png "Säkerhet")

    ![Enkel inloggning](./media/zendesk-tutorial/ic773090.png "Enkel inloggning")

    a. Klicka på fliken för **administratörer och agenter**.

    b. Välj **Enkel inloggning (SSO) och SAML** och välj sedan **SAML**.

    c. I textrutan **SAML SSO URL** klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I textrutan för **URL för fjärrutloggning** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    e. I textrutan **Certificate Fingerprint** (Fingeravtryck för certifikat) klistrar du in **tumavtrycksvärdet** för certifikatet som du har kopierat från Azure-portalen.

    f. Klicka på **Spara**.

### <a name="create-zendesk-test-user"></a>Skapa Zendesk-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Zendesk. Zendesk stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](Zendesk-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

> [!NOTE]
> **Slutanvändarkonton** tillhandahålls automatiskt när du loggar in. Kontona **Agent** och **Admin** måste tillhandahållas manuellt i **Zendesk** innan du loggar in.

1. Logga in på din **Zendesk-hyresgäst.**

2. Välj fliken **Kundlista**.

3. Välj fliken **Användare** och klicka på **Lägg till**.

    ![Lägg till användare](./media/zendesk-tutorial/ic773632.png "Lägga till användare")
4. Skriv **namnet** och **e-postadressen** för ett befintligt Azure AD-konto du vill etablera och klicka sedan på **Spara**.

    ![Ny användare](./media/zendesk-tutorial/ic773633.png "Ny användare")

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa Zendesk för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Zendesk-panelen i åtkomstpanelen så borde du automatiskt loggas in på den Zendesk som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Zendesk med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar Zendesk med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Konfigurera etablering av användare](zendesk-provisioning-tutorial.md)