---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Jisc Student Voter Registration | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jisc Student Voter Registration.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a95d016d-ec53-4b3c-9a51-e0cac0791e49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7de54b507630174107994f1434f17120f3abffbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74807474"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jisc-student-voter-registration"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Jisc Student Voter Registration

I den här självstudien får du lära dig hur du integrerar Jisc Student Voter Registration med Azure Active Directory (Azure AD). När du integrerar Jisc Student Voter Registration med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Jisc Student Voter Registration.
* Gör att användarna automatiskt loggas in på Jisc Student Voter Registration med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Jisc Student Voter Registration enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Jisc Student Voter Registration stöder **SP** initierade SSO
* Jisc Student Väljare Registrering stöder **Just In Time** användare etablering

## <a name="adding-jisc-student-voter-registration-from-the-gallery"></a>Lägga jisc student väljare registrering från galleriet

Om du vill konfigurera integreringen av Jisc Student Voter Registration i Azure AD måste du lägga till Jisc Student Voter Registration från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Jisc Student Voter Registration** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Jisc Student Väljare registrering** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jisc-student-voter-registration"></a>Konfigurera och testa azure AD enkel inloggning för Jisc Student Voter Registration

Konfigurera och testa Azure AD SSO med Jisc Student Voter Registration med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Jisc Student Voter Registration.

Om du vill konfigurera och testa Azure AD SSO med Jisc Student Voter Registration slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Jisc Student Voter Registration SSO](#configure-jisc-student-voter-registration-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Testanvändare för registrering av Jisc-deltagare](#create-jisc-student-voter-registration-test-user)** - om du vill ha en motsvarighet till B.Simon i Jisc Student Voter Registration som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan Integrering **av** Programregistrering för **Jisc Student Väljare** och väljer enkel **inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL i textrutan **Sign-on-URL:**`https://www.studentvoterregistration.ac.uk/consent`

1. Jisc Student Voter Registration ansökan förväntar sig SAML påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar till din SAML token attribut konfiguration. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar jisc Student Voter Registration ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | ---------|  --------- |
    | Postnummer | användare.postkod |
    | Unik användaridentifierare | user.objectid |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera jisc-väljare.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jisc Student Voter Registration.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Jisc Student Voter Registration**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-jisc-student-voter-registration-sso"></a>Konfigurera Jisc Student Väljare Registrering SSO

Om du vill konfigurera enkel inloggning på **Jisc Student Voter Registration-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Jisc Student Voter Registration supportteam](mailto:studentvote@jisc.ac.uk). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-jisc-student-voter-registration-test-user"></a>Skapa testanvändare för registrering av jisc-studentväljare

I det här avsnittet skapas en användare som heter B.Simon i Jisc Student Voter Registration. Jisc Student Voter Registration stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Jisc Student Voter Registration skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Jisc Student Voter Registration i åtkomstpanelen ska du automatiskt loggas in på den Jisc Student Voter Registration som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Jisc Student Väljare registrering med Azure AD](https://aad.portal.azure.com/)