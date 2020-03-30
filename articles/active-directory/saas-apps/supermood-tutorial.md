---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Supermood | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: acb3704541e4c573da4d5331ee10a9c7256162e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75640167"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-supermood"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Supermood

I den här självstudien får du lära dig hur du integrerar Supermood med Azure Active Directory (Azure AD). När du integrerar Supermood med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Supermood.
* Gör att användarna automatiskt loggas in i Supermood med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Supermood enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Supermood stöder **SP och IDP** initierade SSO
* Supermood stöder just in time-användaretablering **Just In Time**

## <a name="adding-supermood-from-the-gallery"></a>Lägga till Supermood från galleriet

Om du vill konfigurera integreringen av Supermood i Azure AD måste du lägga till Supermood från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Supermood** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Supermood** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-supermood"></a>Konfigurera och testa en azure AD-inloggning för Supermood

Konfigurera och testa Azure AD SSO med Supermood med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Supermood.

Om du vill konfigurera och testa Azure AD SSO med Supermood slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Supermood SSO](#configure-supermood-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Supermood-testanvändare](#create-supermood-test-user)** – om du vill ha en motsvarighet till B.Simon i Supermood som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    a. Kontrollera **Ange ytterligare webbadresser**.
    
    b. Skriv en URL i textrutan **Relay State:**`https://supermood.co/auth/sso/saml20`

1. Klicka på **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://supermood.co/app/#!/loginv2`

1. Klicka på **Spara**.

1. Supermood-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovan förväntar sig Supermood ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| ------|
    | firstName | user.givenname |
    | lastName | user.surname |

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Supermood.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Supermood**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-supermood-sso"></a>Konfigurera Supermood SSO

1. Gå till administratörspanelen för Supermood.co som säkerhetsadministratör.

1. Klicka på **Mitt konto** (längst ned till vänster) och Enkel **inloggning (SSO)**.

    ![Certifikatet enda](./media/supermood-tutorial/tutorial_supermood_single.png)

1. Klicka på **Lägg till en SAML 2.0-konfiguration för en e-postdomän i DINA SAML 2.0-konfigurationer.** **Your SAML 2.0 configurations**

    ![Tillägget av certifikat](./media/supermood-tutorial/tutorial_supermood_add.png)

1. På **Lägg till en SAML 2.0-konfiguration för en e-postdomän**. utföra följande steg:

    ![Intyget saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Skriv domänen i **textrutan för den här identitetsprovidern för den här identitetsprovidern.**

    b. I textrutan **Använd en metadata-URL** klistrar du in **url:en för metadatametadata** som du har kopierat från Azure-portalen.

    c. Klicka på **Lägg till**.

### <a name="create-supermood-test-user"></a>Skapa Supermood-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Supermood. Supermood stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Supermood skapas en ny efter autentisering. Om du behöver skapa en användare manuellt kontaktar du [Supermoods supportteam](mailto:hello@supermood.fr).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på supermoodpanelen på åtkomstpanelen ska du automatiskt loggas in på supermood som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Supermood med Azure AD](https://aad.portal.azure.com/)