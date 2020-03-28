---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med I händelse av kris - Mobil | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och In Case of Crisis - Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6afa98e9-a123-4cbb-add6-afcefc1242e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bff484e4ca3a1579067501be134d71da72abfa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72266089"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---mobile"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med I händelse av kris - Mobil

I den här självstudien får du lära dig hur du integrerar I händelse av kris – mobil med Azure Active Directory (Azure AD). När du integrerar I händelse av kris – mobil med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till In Case of Crisis - Mobile.
* Gör det möjligt för användarna att automatiskt loggas in på In Case of Crisis - Mobile med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* I händelse av kris - Mobil enkel inloggning (SSO) aktiverat abonnemang.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* I händelse av kris - Mobile stöder **IDP** initierad SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-in-case-of-crisis---mobile-from-the-gallery"></a>Lägga till i händelse av kris - Mobil från galleriet

För att konfigurera integreringen av In Case of Crisis - Mobile i Azure AD måste du lägga till I händelse av kris - Mobil från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv I avsnittet **Lägg till från galleriet** skriver du **I händelse av kris - Mobil** i sökrutan.
1. Välj **I händelse av kris - Mobil** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---mobile"></a>Konfigurera och testa en enda Azure AD-inloggning för In Case of Crisis - Mobile

Konfigurera och testa Azure AD SSO med In Case of Crisis - Mobile med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i In Case of Crisis - Mobile.

Så här konfigurerar och testar du Azure AD SSO med I händelse av kris – mobil slutför du följande byggstenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera I händelse av kris - Mobile SSO](#configure-in-case-of-crisis---mobile-sso)** - för att konfigurera de enskilda inloggningsinställningarna på applikationssidan.
    1. **[Skapa i händelse av kris - Mobil testanvändare](#create-in-case-of-crisis---mobile-test-user)** - att ha en motsvarighet till B.Simon i vid kris - Mobil som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **In Case of Crisis - Mobile** Hantera på sidan Hantera på sidan **Manage** [Azure-portalen](https://portal.azure.com/)på sidan I händelse av kris - Integrering av mobila program och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP-initierat** läge och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.** 

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Navigera till avsnittet **Hantera** till vänster på sidan, klicka på **fliken Egenskaper** och kopiera sedan **webbadressen för användaråtkomst** och spara den på datorn.

    ![Egenskaper för enkel inloggning](./media/in-case-of-crisis-mobile-tutorial/properties.png)

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till In Case of Crisis - Mobile.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **I händelse av kris - Mobil**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-in-case-of-crisis---mobile-sso"></a>Konfigurera i händelse av kris - Mobil SSO

Om du vill konfigurera enkel inloggning i **händelse av kris – mobil** sida måste du skicka det nedladdade **certifikatet (Raw)** och kopierad url för **användaråtkomst** från Azure-portalen till [I händelse av kris - Mobil supportteam](https://www.rockdovesolutions.com/features/enterprise-ready). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-in-case-of-crisis---mobile-test-user"></a>Skapa i händelse av kris - Mobil testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i In Case of Crisis - Mobile. Arbeta med [I händelse av kris - Mobilt supportteam](https://www.rockdovesolutions.com/features/enterprise-ready) för att lägga till användarna i infall av kris - Mobil plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen I händelse av kris - Mobil i åtkomstpanelen ska du automatiskt loggas in på den I händelse av kris - Mobil som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova i händelse av kris - Mobil med Azure AD](https://aad.portal.azure.com/)

