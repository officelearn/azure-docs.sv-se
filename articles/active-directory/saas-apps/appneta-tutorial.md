---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med AppNeta Performance Monitor | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac9e1d32e0280bcf053578aa102cc6fd200a4b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561246"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med AppNeta Performance Monitor

I den här självstudien får du lära dig hur du integrerar AppNeta Performance Monitor med Azure Active Directory (Azure AD). När du integrerar AppNeta Performance Monitor med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AppNeta Performance Monitor.
* Gör att användarna automatiskt loggas in på AppNeta Performance Monitor med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* AppNeta Performance Monitor enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* AppNeta Performance Monitor stöder **SP**-initierad enkel inloggning


* AppNeta Performance Monitor stöder **just-in-time**-användaretablering

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Lägga till AppNeta Performance Monitor från galleriet

För att konfigurera integrering av AppNeta Performance Monitor med Azure AD behöver du lägga till AppNeta Performance Monitor från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **AppNeta Performance Monitor** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **AppNeta Performance Monitor** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-appneta-performance-monitor"></a>Konfigurera och testa azure AD-enkel inloggning för AppNeta Performance Monitor

Konfigurera och testa Azure AD SSO med AppNeta Performance Monitor med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i AppNeta Performance Monitor.

Om du vill konfigurera och testa Azure AD SSO med AppNeta Performance Monitor slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera AppNeta Performance Monitor SSO](#configure-appneta-performance-monitor-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Testanvändare för AppNeta Performance Monitor](#create-appneta-performance-monitor-test-user)** – om du vill ha en motsvarighet till B.Simon i AppNeta Performance Monitor som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Programintegrering av AppNeta Performance Monitor** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.pm.appneta.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du ett värde: `PingConnect`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för AppNeta Performance Monitor](mailto:support@appneta.com) och be om det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. AppNeta Performance Monitor-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig AppNeta Performance Monitor-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | --------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | e-post| user.userprincipalname|
    | namn| user.userprincipalname|
    | grupper  | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user.jobtitle|
    | | |

    > [!NOTE]
    > **grupper** refererar till den säkerhetsgrupp i Appneta som mappas till en **roll** i Azure AD. I [det här](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) dokumentet beskrivs hur du skapar anpassade roller i Azure AD.

    1. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    1. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    1. Lämna **Namnrymd** tom.

    1. Välj Källa som **Attribut**.

    1. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    1. Klicka på **Ok**

    1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera AppNeta-prestandaövervakare.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AppNeta Performance Monitor.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **AppNeta Performance Monitor**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-appneta-performance-monitor-sso"></a>Konfigurera SSO för Prestandaövervakare för AppNeta

För att konfigurera enkel inloggning på **AppNeta Performance Monitor**-sidan behöver du skicka nedladdade **XML-federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för AppNeta Performance Monitor](mailto:support@appneta.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-appneta-performance-monitor-test-user"></a>Skapa AppNeta Performance Monitor-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i AppNeta Performance Monitor. AppNeta Performance Monitor stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i AppNeta Performance Monitor skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [Supportteamet för AppNeta Performance Monitor](mailto:support@appneta.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på AppNeta Performance Monitor-panelen i åtkomstpanelen bör du automatiskt loggas in på AppNeta Performance Monitor som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova AppNeta Performance Monitor med Azure AD](https://aad.portal.azure.com/)

