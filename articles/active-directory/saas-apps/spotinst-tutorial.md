---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Spotinst | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5490ff6c6143dff258d74e013bb9d4c821aab625
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263293"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Spotinst

I den här självstudien får du lära dig hur du integrerar Spotinst med Azure Active Directory (Azure AD). När du integrerar Spotinst med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Spotinst.
* Gör att användarna automatiskt loggas in på Spotinst med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Spotinst enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Spotinst stöder **SP och IDP** initierade SSO

## <a name="adding-spotinst-from-the-gallery"></a>Lägga till Spotinst från galleriet

Om du vill konfigurera integreringen av Spotinst i Azure AD måste du lägga till Spotinst från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv Spotinst i sökrutan i avsnittet Lägg till från **galleriet.** **Add from the gallery**
1. Välj **Spotinst** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Konfigurera och testa en azure AD-inloggning för Spotinst

Konfigurera och testa Azure AD SSO med Spotinst med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Spotinst.

Om du vill konfigurera och testa Azure AD SSO med Spotinst slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Spotinst SSO](#configure-spotinst-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Spotinst-testanvändare](#create-spotinst-test-user)** – om du vill ha en motsvarighet till B.Simon i Spotinst som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Spotinst** Hantera på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    a. Kontrollera **Ange ytterligare webbadresser**.

    b. Skriv ett värde i textrutan **Relay State:**`<ID>`

1. Klicka på **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > Relay State-värdet är inte verkligt. Du kommer att uppdatera relay state-värdet med det faktiska relay state-värdet, vilket förklaras senare i självstudien.

1. Klicka på **Spara**.

1. Spotinst-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Spotinst-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | -----| --------------- |
    | E-post | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera spotinst** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Spotinst.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Spotinst**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-spotinst-sso"></a>Konfigurera Spotinst SSO

1. I ett annat webbläsarfönster loggar du in på Spotinst som säkerhetsadministratör.

2. Klicka på **användarikonen** längst upp till höger på skärmen och klicka på **Inställningar**.

    ![Inställningar för Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Klicka på fliken **SÄKERHET** högst upp och välj sedan **Identitetsleverantörer** och utför följande steg:

    ![Spotinst säkerhet](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopiera **relay state-värdet** för din instans och klistra in det i relay **state-textrutan** i avsnittet Grundläggande **SAML-konfiguration** på Azure-portalen.

    b. Klicka på **BLÄDDRA** om du vill ladda upp xml-filen för metadata som du har hämtat från Azure-portalen

    c. Klicka på **SPARA**.

### <a name="create-spotinst-test-user"></a>Skapa Spotinst-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i Spotinst.

1. Om du har konfigurerat programmet i startläge för **SP** gör du följande:

   a. I ett annat webbläsarfönster loggar du in på Spotinst som säkerhetsadministratör.

   b. Klicka på **användarikonen** längst upp till höger på skärmen och klicka på **Inställningar**.

    ![Inställningar för Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klicka på **Användare** och välj **LÄGG TILL ANVÄNDARE**.

    ![Inställningar för Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Gör följande i avsnittet Lägg till användare:

    ![Inställningar för Spotinst](./media/spotinst-tutorial/adduser2.png)

    * I textrutan **Fullständigt namn** anger du det fullständiga namnet på användaren som **BrittaSimon**.

    * I **textrutan E-post** anger du e-postadressen till den användare som `brittasimon\@contoso.com`.

    * Välj din organisationsspecifika information för **organisationsrollen, kontorollen och kontona**.

2. Om du har konfigurerat programmet i **idp-initierat** läge finns det inget åtgärdsobjekt för dig i det här avsnittet. Spotinst stöder just-in-time-etablering, vilket är aktiverat som standard. En ny användare skapas under ett försök att komma åt Spotinst om den inte finns ännu.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Spotinst på åtkomstpanelen ska du automatiskt loggas in på den spotinst som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Spotinst med Azure AD](https://aad.portal.azure.com/)

