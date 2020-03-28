---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Splashtop | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Splashtop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c05f63c2-4170-49ce-a967-be1cb1dbcd06
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6ecb03130e26d432f0bd10980c7c3553ce9f8b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77539788"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Splashtop

I den här självstudien får du lära dig hur du integrerar Splashtop med Azure Active Directory (Azure AD). När du integrerar Splashtop med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Splashtop.
* Gör att användarna automatiskt loggas in på Splashtop med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Splashtop enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Splashtop stöder **SP** initierade SSO

* När du har konfigurerat Splashtop kan du framtvinga sessionskontroller som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Lägga till Splashtop från galleriet

Om du vill konfigurera integreringen av Splashtop i Azure AD måste du lägga till Splashtop från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Splashtop** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Splashtop** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Konfigurera och testa en azure AD-inloggning för Splashtop

Konfigurera och testa Azure AD SSO med Splashtop med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Splashtop.

Om du vill konfigurera och testa Azure AD SSO med Splashtop slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Splashtop SSO](#configure-splashtop-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Splashtop-testanvändare](#create-splashtop-test-user)** – om du vill ha en motsvarighet till B.Simon i Splashtop som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Splashtop**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    I rutan **Inloggnings-URL** anger du följande URL: `https://my.splashtop.com/login/sso`

1. Splashtop-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar listan över standardattribut, medan **namnidentifieraren** mappas med **userprincipalname**. TicketManager-programmet förväntar sig att **namnidentifieraren** mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Splashtop** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Splashtop.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Splashtop**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-splashtop-sso"></a>Konfigurera Splashtop SSO

I det här avsnittet måste du ansöka om en ny SSO-metod från [Splashtop webbportal](https://my.splashtop.com/login).
1. Gå till fliken **Kontoinformation** / **Team** på välkomstportalen och bläddra nedåt för att hitta avsnittet **Enkel inloggning.** Klicka sedan på **Använd för ny SSO-metod**.

    ![image](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Ange ett **SSO-namn**i användningsfönstret . Till exempel Ny Azure och välj sedan **Azure** som IDP-typ och infoga **inloggnings-URL** och **Azure AD-identifierare** kopieras från Splashtop-programmet på Azure-portalen.

    ![image](media/splashtop-tutorial/azure-sso-1.png)

1. Om du vill ha information om certifikat högerklickar du på cert-filen som hämtats från Splashtop-programmet på Azure-portalen, redigerar den med Anteckningar och kopierar sedan innehållet, klistrar in det i fältet **Hämta certifikat (Base64).**

    ![](media/splashtop-tutorial/cert-1.png) ![](media/splashtop-tutorial/cert-2.png) bildbild ![](media/splashtop-tutorial/azure-sso-2.png)

1. Klart! Klicka på **Spara** och Splashtop SSO valideringsteam kommer att kontakta dig för verifieringsinformation och sedan aktivera SSO-metoden.

### <a name="create-splashtop-test-user"></a>Skapa Splashtop-testanvändare

1. När SSO-metoden har aktiverats kontrollerar du den nyskapade SSO-metoden för att aktivera den i avsnittet **Enkel inloggning.**

    ![image](media/splashtop-tutorial/enable.png)

1. Bjud in testanvändaren `B.Simon@contoso.com` till exempel till splashtop-teamet med den nyskapade SSO-metoden.

    ![image](media/splashtop-tutorial/invite.png)

1. Du kan också ändra ett befintligt Splashtop-konto till ett SSO-konto, se [instruktioner](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Klart! Du kan använda SSO-kontot för att logga in på Splashtop webbportal eller Splashtop Business-app.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Splashtop-panelen på åtkomstpanelen ska du automatiskt loggas in på splashtop som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Splashtop med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar Splashtop med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)