---
title: 'Självstudiekurs: Azure Active Directory-integrering med Vtiger CRM (SAML) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vtiger CRM (SAML).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f14e34a6-f51f-4cd1-a6ad-f04df551303d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45782d7827a289cc0942eca61d6b30d071a6721c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67450746"
---
# <a name="tutorial-integrate-vtiger-crm-saml-with-azure-active-directory"></a>Självstudiekurs: Integrera Vtiger CRM (SAML) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Vtiger CRM (SAML) med Azure Active Directory (Azure AD). När du integrerar Vtiger CRM (SAML) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Vtiger CRM (SAML).
* Aktivera dina användare automatiskt inloggade på Vtiger CRM (SAML) med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Vtiger CRM (SAML) enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. 

* Vtiger CRM (SAML) stöder **SP** initierade SSO
* Vtiger CRM (SAML) stöder just in time-användaretablering **Just In Time**

## <a name="adding-vtiger-crm-saml-from-the-gallery"></a>Lägga till Vtiger CRM (SAML) från galleriet

Om du vill konfigurera integreringen av Vtiger CRM (SAML) i Azure AD måste du lägga till Vtiger CRM (SAML) från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Vtiger CRM (SAML)** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Vtiger CRM (SAML)** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Vtiger CRM (SAML) med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Vtiger CRM (SAML).

Så här konfigurerar och testar du Azure AD SSO med Vtiger CRM (SAML) och slutför följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Vtiger CRM (SAML) SSO](#configure-vtiger-crm-saml-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Vtiger CRM (SAML) testanvändare](#create-vtiger-crm-saml-test-user)** - att ha en motsvarighet till Britta Simon i Vtiger CRM (SAML) som är kopplad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)på **sidan Vtiger CRM (SAML)** och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

    | | |
    | - |- |
    | `https://<customer_instance>.od1.vtiger.com` |
    | `https://<customer_instance>.od2.vtiger.com` |
    | `https://<customer_instance>.od1.vtiger.ws` |

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<customer_instance>.od1.vtiger.com/sso/saml?acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Vtiger CRM (SAML) Client support team](mailto:support@vtiger.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Vtiger CRM (SAML)** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-vtiger-crm-saml-sso"></a>Konfigurera SSO (Vtiger CRM)

Om du vill konfigurera saml-sidan (Single Sign-on on **Vtiger CRM)** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure Portal till [Vtiger CRM (SAML) supportteam](mailto:support@vtiger.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Vtiger CRM (SAML).

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Vtiger CRM (SAML) i**programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-vtiger-crm-saml-test-user"></a>Skapa Vtiger CRM-testanvändare (SAML)

I det här avsnittet skapas en användare som heter Britta Simon i Vtiger CRM (SAML). Vtiger CRM (SAML) stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Vtiger CRM (SAML) skapas en ny efter autentisering.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Vtiger CRM (SAML) på åtkomstpanelen ska du automatiskt loggas in på den Vtiger CRM (SAML) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)