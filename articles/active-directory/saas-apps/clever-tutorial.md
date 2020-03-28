---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Clever | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6884445b49b518654ccb1484b024728c4feb2b51
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75689651"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clever"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Smart

I den här självstudien får du lära dig hur du integrerar Clever med Azure Active Directory (Azure AD). När du integrerar Clever med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Clever.
* Gör det möjligt för användarna att automatiskt loggas in på Clever med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Smart enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Clever har stöd för **SP**-initierad enkel inloggning

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-clever-from-the-gallery"></a>Lägga till Clever från galleriet

För att konfigurera integreringen av Clever i Azure AD måste du lägga till Clever från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Smart** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Smart** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-clever"></a>Konfigurera och testa en azure AD-inloggning för Smart

Konfigurera och testa Azure AD SSO med Clever med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Clever.

Om du vill konfigurera och testa Azure AD SSO med Clever slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Smart SSO](#configure-clever-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa smart testanvändare](#create-clever-test-user)** - att ha en motsvarighet till B.Simon i Clever som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Clever** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://clever.com/in/<companyname>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för Clever](https://clever.com/about/contact/) om be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Clever.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Clever**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-clever-sso"></a>Konfigurera Smart SSO

1. I ett annat webbläsarfönster loggar du in på din smart företagswebbplats som administratör.

1. I verktygsfältet klickar du på **Instant Login** (Direkt inloggning).

    ![Omedelbar inloggning](./media/clever-tutorial/ic798984.png "Omedelbar inloggning")

    > [!NOTE]
    > Innan du kan testa enkel inloggning måste du kontakta [kundsupporten för Clever](https://clever.com/about/contact/) och aktivera enkel inloggning för Office 365 i serverdelen.

1. På sidan **Instant Login** (Direkt inloggning) utför du följande steg:
 
    ![Omedelbar inloggning](./media/clever-tutorial/ic798985.png "Omedelbar inloggning")

    a. Skriv **inloggnings-URL**.

    >[!NOTE]
    >**Inloggnings-URL** är ett anpassat värde. Kontakta [kundsupporten för Clever](https://clever.com/about/contact/) om be om det här värdet.

    b. Som **Identity System** (Identitetssystemet) väljer du **ADFS**.

    c. I textrutan **Metadata-URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**.

### <a name="create-clever-test-user"></a>Skapa Clever-testanvändare

För att Azure AD-användare ska kunna logga in på Clever måste de etableras i Clever.

När det gäller Clever kontaktar du [kundsupporten för Clever](https://clever.com/about/contact/) för att lägga till användarna i Clever-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för Clever-kontoskapande som tillhandahålls av Clever för att etablera AAD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Clever-panelen i åtkomstpanelen bör du automatiskt loggas in på Clever som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Clever med Azure AD](https://aad.portal.azure.com/)