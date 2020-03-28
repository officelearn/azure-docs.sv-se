---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Versal | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Versal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5e5a3f8070829916b228e9bc68e37156ddf5f6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561793"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-versal"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Versal

I den här självstudien får du lära dig hur du integrerar Versal med Azure Active Directory (Azure AD). När du integrerar Versal med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Versal.
* Gör att användarna automatiskt loggas in på Versal med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Versal enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.


* Versal stöder **IDP** initierad SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-versal-from-the-gallery"></a>Lägga till Versal från galleriet

Om du vill konfigurera integreringen av Versal i Azure AD måste du lägga till Versal från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Versal** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Versal** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-versal"></a>Konfigurera och testa en azure AD-inloggning för Versal

Konfigurera och testa Azure AD SSO med Versal med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Versal.

Så här konfigurerar och testar du Azure AD SSO med Versal:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Versal SSO](#configure-versal-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Versal-testanvändare](#create-versal-test-user)** - om du vill ha en motsvarighet till B.Simon i Versal som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Versal** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. Skriv en URL i textrutan **Identifierare:**`VERSAL`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Kontakta [Versal Client supportteam](https://support.versal.com/hc/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Versal-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Versal-programmet förväntar sig att **namnidentifieraren** mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Versal** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Versal.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Versal**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-versal-sso"></a>Konfigurera Versal SSO

Om du vill konfigurera enkel inloggning på **Versal-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Versal-supportteamet](https://support.versal.com/hc/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-versal-test-user"></a>Skapa versaltestanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Versal. Följ [användarsupportguiden för att skapa ett SAML-test](https://support.versal.com/hc/articles/115011672887-Creating-a-SAML-test-user) för att skapa användaren B.Simon i organisationen. Användare måste skapas och aktiveras i Versal innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD-konfiguration med enkel inloggning med hjälp av en Versal-kurs inbäddad på din webbplats.
Se guiden [Bädda in organisationskurser](https://support.versal.com/hc/articles/203271866-Embedding-organizational-courses) **SAML Single Sign-On** för instruktioner om hur du bäddar in en Versal-kurs med stöd för enkel inloggning i Azure AD. 

Du måste skapa en kurs, dela den med din organisation och publicera den för att testa kursinbäddning. Se [Skapa en kurs,](https://support.versal.com/hc/articles/203722528-Create-a-course) [Publicera en kurs](https://support.versal.com/hc/articles/203753398-Publishing-a-course)och [Kurs- och elevhantering](https://support.versal.com/hc/articles/206029467-Course-and-learner-management) för mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Versal med Azure AD](https://aad.portal.azure.com/)

