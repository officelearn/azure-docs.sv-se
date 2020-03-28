---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Leapsome | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28b95e779e2b814b0ae91059c3edd12644d7c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430944"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Leapsome

I den här självstudien får du lära dig hur du integrerar Leapsome med Azure Active Directory (Azure AD). När du integrerar Leapsome med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Leapsome.
* Gör att användarna automatiskt loggas in på Leapsome med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Leapsome single sign-on (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Leapsome stöder **SP och IDP** initierade SSO

## <a name="adding-leapsome-from-the-gallery"></a>Lägga Leapsome från galleriet

Om du vill konfigurera integreringen av Leapsome i Azure AD måste du lägga till Leapsome från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Leapsome** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Leapsome** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Konfigurera och testa en azure AD-inloggning för Leapsome

Konfigurera och testa Azure AD SSO med Leapsome med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Leapsome.

Om du vill konfigurera och testa Azure AD SSO med Leapsome slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Leapsome SSO](#configure-leapsome-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Leapsome-testanvändare](#create-leapsome-test-user)** – om du vill ha en motsvarighet till B.Simon i Leapsome som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Leapsome-programintegration** på **Manage** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL i textrutan **Identifierare:**`https://www.leapsome.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > Föregående svars-URL och url-värde för inloggning är inte verkligt värde. Du kommer att uppdatera dessa med de faktiska värdena, vilket förklaras senare i självstudien.

1. Leapsome-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar Leapsome ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut | Namnområde |
    | ---------------| --------------- | --------- |  
    | förnamn | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | efternamn | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | bild | URL till medarbetarens bild | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > Värdet för bildattributet är inte verkligt. Uppdatera det här värdet med faktisk bild-URL. Kontakta [Leapsome Client support team](mailto:support@leapsome.com)för att få det här värdet .

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Leapsome** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Leapsome.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Leapsome**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-leapsome-sso"></a>Konfigurera Leapsome SSO

1. I ett annat webbläsarfönster loggar du in på Leapsome som säkerhetsadministratör.

1. Klicka på logotyp för inställningar längst upp till höger och klicka sedan på **Admininställningar**.

    ![Leapsome set](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Klicka på **SSO (Single Sign On)** i den vänstra menyraden och utför följande steg på sidan **SAML-baserad enkel inloggning (SSO):**

    ![Leapsome saml (Leapsome saml)](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Välj **Aktivera SAML-baserad enkel inloggning**.

    b. Kopiera **värdet för inloggnings-URL:en (peka dina användare här för att starta inloggning)** och klistra in det i textrutan **Sign-on URL** i avsnittet Grundläggande **SAML-konfiguration** på Azure-portalen.

    c. Kopiera **svars-URL:en (får svar från din identitetsprovider)** och klistra in det i textrutan **Svara URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    d. I textrutan **SSO-inloggningsadress (tillhandahålls av identitetsprovidern)** klistrar du in värdet **för inloggnings-URL**, som du kopierade från Azure-portalen.

    e. Kopiera certifikatet som du har hämtat från Azure-portalen utan `--BEGIN CERTIFICATE and END CERTIFICATE--` kommentarer och klistra in det i textrutan Certifikat **(tillhandahålls av identitetsprovidern).**

    f. Klicka på **UPPDATERA SSO-INSTÄLLNINGAR**.

### <a name="create-leapsome-test-user"></a>Skapa Leapsome-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Leapsome. Arbeta med [Leapsome Client support team](mailto:support@leapsome.com) för att lägga till användare eller domänen som måste läggas till i en tillåt lista för Leapsome-plattformen. Om domänen läggs till av teamet etableras användarna automatiskt till Leapsome-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Leapsome på åtkomstpanelen ska du automatiskt loggas in på den Leapsome som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Leapsome med Azure AD](https://aad.portal.azure.com/)