---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Upshotly | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Upshotly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9ef54a33-da2d-4ec2-9943-a4fb6aee5e9c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 1/7/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f82b7ce417e79cef29c9e852ca15a8ea1e8d62f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76268155"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upshotly"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Upshotly

I den här självstudien får du lära dig hur du integrerar Upshotly med Azure Active Directory (Azure AD). När du integrerar Upshotly med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Upshotly.
* Gör att användarna automatiskt loggas in på Upshotly med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En av dem har aktiverat prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Upshotly stöder **SP och IDP** initierade SSO

## <a name="adding-upshotly-from-the-gallery"></a>Lägga till resultat från galleriet

Om du vill konfigurera integreringen av Upshotly i Azure AD måste du lägga till Upshotly från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv Snabbt i sökrutan i avsnittet Lägg till från **galleriet.** **Add from the gallery**
1. Välj **Upp till resultatet från** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-upshotly"></a>Konfigurera och testa en azure AD-inloggning för upshotly

Konfigurera och testa Azure AD SSO med Upshotly med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Upshotly.

Om du vill konfigurera och testa Azure AD SSO med Upshotly slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Upshotly SSO](#configure-upshotly-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa upshotly testanvändare](#create-upshotly-test-user)** - att ha en motsvarighet till B.Simon i Upshotly som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Upshotly** Hantera på sidan Hantera på sidan **Manage** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga url:erna redan är förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://app.upshotly.com/api/sso/login/<companyID>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera de här värdena med den faktiska inloggnings-URL:en. Du kommer att få **företagetsID-värde** förklaras senare i handledningen. Kontakta [Upshotly Client support team](mailto:support@upshotly.com) för frågor. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera resultatlöst** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Upshotly.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Upshotly**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-upshotly-sso"></a>Konfigurera upp till resultatet SSO

1. I ett annat webbläsarfönster loggar du in på företagets webbplats upshotly som administratör.

1. Klicka på **användarprofilen** och navigera till **Admin > SSO** och utför följande steg:

    ![Konfiguration med resultat av resultat](./media/upshotly-tutorial/config1.png)

    a. Kopiera **värdet företags-ID** och använd det här **företags-ID-värdet** för att ersätta **företags-ID-värdet** som finns i **inloggnings-URL:en** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Öppna nedladdad **FEDERATION metadata XML** från Azure portal till Anteckningar, kopiera innehållet i metadata XML och klistra in den i **XML-metadata** textruta.

### <a name="create-upshotly-test-user"></a>Skapa testanvändare med resultatresultat

I det här avsnittet skapar du en användare som heter B.Simon i Upshotly Edge Cloud. Arbeta med [Supportteamet för Upshotly-klienten](mailto:support@upshotly.com) för att lägga till användarna i plattformen Upshotly Edge Cloud. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Snabbt på åtkomstpanelen ska du automatiskt loggas in på den resultatsida som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova upshotly med Azure AD](https://aad.portal.azure.com/)