---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med LinkedIn Sales Navigator | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn Sales Navigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d8293b23559860e70191576db13c3cd14f520e6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430890"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med LinkedIn Sales Navigator

I den här självstudien får du lära dig hur du integrerar LinkedIn Sales Navigator med Azure Active Directory (Azure AD). När du integrerar LinkedIn Sales Navigator med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LinkedIn Sales Navigator.
* Gör att användarna automatiskt loggas in på LinkedIn Sales Navigator med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En SSO-prenumeration (LinkedIn Sales Navigator single sign-on).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* LinkedIn Sales Navigator stöder **SP och IDP** initierad SSO
* LinkedIn Sales Navigator stöder just in time-användaretablering **Just In Time**
* LinkedIn Sales Navigator stöder [ **automatisk** användaretablering](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Lägga till LinkedIn Sales Navigator från galleriet

Om du vill konfigurera integreringen av LinkedIn Sales Navigator i Azure AD måste du lägga till LinkedIn Sales Navigator från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **LinkedIn Sales Navigator** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **LinkedIn Sales Navigator** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Konfigurera och testa en azure AD-inloggning för LinkedIn Sales Navigator

Konfigurera och testa Azure AD SSO med LinkedIn Sales Navigator med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i LinkedIn Sales Navigator.

Så här konfigurerar och testar du Azure AD SSO med LinkedIn Sales Navigator:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera LinkedIn Sales Navigator SSO](#configure-linkedin-sales-navigator-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa LinkedIn Sales Navigator testanvändare](#create-linkedin-sales-navigator-test-user)** - om du vill ha en motsvarighet till B.Simon i LinkedIn Sales Navigator som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **LinkedIn** Sales **Navigator-programintegrering**på [Azure](https://portal.azure.com/)Portal och välj enkel inloggning . **LinkedIn Sales Navigator**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare** anger du värdet **för entitets-ID,** du kopierar enhets-ID-värdet från Linkedin-portalen som förklaras senare i den här självstudien.

    b. I textrutan **Svara URL** anger du **url-värdet För kontrollkonsumentåtkomst (ACS),** kopierar du URL-värdet för Kontroll konsumentåtkomst (ACS) från Linkedin-portalen som förklaras senare i den här självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. LinkedIn Sales Navigator-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig LinkedIn Sales Navigator-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | --- | --- |
    | e-post| user.mail |
    | avdelning| user.department |
    | förnamn| user.givenname |
    | efternamn| user.surname |
    | Unik användaridentifierare | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera LinkedIn Sales Navigator** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LinkedIn Sales Navigator.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **LinkedIn Sales Navigator**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-linkedin-sales-navigator-sso"></a>Konfigurera LinkedIn Sales Navigator SSO

1. I ett annat webbläsarfönster loggar du in på din **LinkedIn Sales Navigator-webbplats** som administratör.

1. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Välj också **Sales Navigator** i listrutan.

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Klicka på **ELLER Klicka här för att läsa in och kopiera enskilda fält från formuläret** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Kopiera **entitets-ID** och klistra in den i textrutan **Identifierare** i **den grundläggande SAML-konfigurationen** i Azure-portalen.

    b. Kopiera **ACS-url (Assertion Consumer Access)** och klistra in den i textrutan **Svara URL** i den grundläggande **SAML-konfigurationen** i Azure-portalen.

1. Gå till avsnittet **LinkedIn-administratörsinställningar**. Ladda upp XML-filen som du har hämtat från Azure-portalen genom att klicka på alternativet **Ladda upp XML-fil.**

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Aktivera enkel inloggning genom att klicka på **På**. SSO-statusen ändras från **Inte ansluten** till **Ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Skapa användare av LinkedIn Sales Navigator-test

Linked Sales Navigator Application stöder JIT-användareetablering (Just in Time) och efter att autentiseringsanvändare har skapats i programmet automatiskt. Aktivera **Tilldela automatiskt licenser** för att tilldela en licens till användaren.

   ![Skapa en testanvändare för Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LinkedIn Sales Navigator på åtkomstpanelen bör du automatiskt loggas in på den LinkedIn Sales Navigator som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova LinkedIn Sales Navigator med Azure AD](https://aad.portal.azure.com/)