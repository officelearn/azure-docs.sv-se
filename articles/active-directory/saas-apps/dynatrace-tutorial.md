---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Dynatrace | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dynatrace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 267ad37f-feda-4fac-bd15-7610174caf45
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 275215a69f97d93a1b1741a8fa8f3ca752c8c9ef
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758977"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dynatrace"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Dynatrace

I den här självstudien får du lära dig hur du integrerar Dynatrace med Azure Active Directory (Azure AD). När du integrerar Dynatrace med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Dynatrace.
* Gör att användarna automatiskt loggas in på Dynatrace med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Dynatrace enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Dynatrace stöder **SP och IDP** initierade SSO
* Dynatrace stöder just in time-användaretablering **Just In Time**

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde. Endast en instans kan konfigureras i en klient.

## <a name="adding-dynatrace-from-the-gallery"></a>Lägga till Dynatrace från galleriet

Om du vill konfigurera integreringen av Dynatrace i Azure AD måste du lägga till Dynatrace från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **Företagsprogram**och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Dynatrace** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Dynatrace** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-dynatrace"></a>Konfigurera och testa en azure AD-inloggning för Dynatrace

Konfigurera och testa Azure AD SSO med Dynatrace med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Dynatrace.

Om du vill konfigurera och testa Azure AD SSO med Dynatrace slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Dynatrace SSO](#configure-dynatrace-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Dynatrace-testanvändare](#create-dynatrace-test-user)** – om du vill ha en motsvarighet till B.Simon i Dynatrace som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Dynatrace-programintegrering** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Dynatrace**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP-initierat** läge och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. Klicka på **Ange ytterligare webbadresser** och slutför följande steg för att konfigurera programmet i **återupptastartat SP-läge:**

    Skriv en URL i textrutan **Sign-on-URL:**`https://sso.dynatrace.com/`

1. Leta reda på XML för **federationsmetadata**i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML.** Välj **Hämta** om du vill hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Öppna dialogrutan **Edit** **SAML-signeringscertifikat** i avsnittet **SAML-signeringscertifikat.** Utför följande steg:

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

    a. Inställningen **Signeringsalternativ** är förifylld. Läs inställningarna enligt din organisation.

    b. Klicka på **Spara**.

    ![Communifire-signeringsalternativ](./media/dynatrace-tutorial/tutorial-dynatrace-signing-option.png)

1. I avsnittet **Konfigurera Dynatrace** kopierar du lämpliga webbadresser baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Dynatrace.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Dynatrace**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-dynatrace-sso"></a>Konfigurera Dynatrace SSO

Om du vill konfigurera enkel inloggning på **Dynatrace-sidan** måste du skicka den nedladdade **XML-filen för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Dynatrace](https://www.dynatrace.com/support/help/shortlink/users-sso-hub). Du kan följa instruktionerna på Dynatrace webbplats för att konfigurera SAML SSO-anslutningen på båda sidor.

### <a name="create-dynatrace-test-user"></a>Skapa Dynatrace-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Dynatrace. Dynatrace stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Dynatrace skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Dynatrace på åtkomstpanelen ska du automatiskt loggas in på Dynatrace, som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Dynatrace med Azure AD](https://aad.portal.azure.com/)
