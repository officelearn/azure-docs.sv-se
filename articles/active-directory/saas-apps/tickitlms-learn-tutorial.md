---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med TickitLMS Learn | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TickitLMS Learn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9ba9d67d-17c0-4dce-bb4d-b89a9d645160
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df5c4cef9c2334328dc1291db95cfed3994d1709
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668904"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tickitlms-learn"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med TickitLMS Learn

I den här självstudien får du lära dig hur du integrerar TickitLMS Learn med Azure Active Directory (Azure AD). När du integrerar TickitLMS Learn med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TickitLMS Learn.
* Gör att användarna automatiskt loggas in på TickitLMS Learn med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* TickitLMS Lär dig en enda inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* TickitLMS Learn stöder **SP och IDP** initierad SSO
* När du har konfigurerat TickitLMS Learn kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tickitlms-learn-from-the-gallery"></a>Lägga till TickitLMS Lär av galleriet

Om du vill konfigurera integreringen av TickitLMS Learn i Azure AD måste du lägga till TickitLMS Learn från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **TickitLMS Learn** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **TickitLMS Lär från** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-tickitlms-learn"></a>Konfigurera och testa azure AD-enkel inloggning för TickitLMS Learn

Konfigurera och testa Azure AD SSO med TickitLMS Lär dig med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i TickitLMS Learn.

Om du vill konfigurera och testa Azure AD SSO med TickitLMS Learn slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera TickitLMS Learn SSO](#configure-tickitlms-learn-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa TickitLMS Learn-testanvändare](#create-tickitlms-learn-test-user)** – om du vill ha en motsvarighet till B.Simon i TickitLMS Lär dig som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan Azure-portalen och välj enkel inloggning på sidan [Azure portal](https://portal.azure.com/) **TickitLMS** **Learn-programintegration**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https:/learn.tickitlms.com/sso/login`

1. Klicka på **Spara**.

1. TickitLMS Learn-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig TickitLMS Learn-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.
    
    | Namn |   |  Källattribut|
    | ---------------| --------------- | --------- |
    | samlaccount (samlaccount) |  | användare.samlaccount |
    | employeeid |  | user.employeeid |
    | roll |  | user.roll |
    | avdelning |  | user.department |
    | rapporterar till |  | user.reportsto användare.reportsto |

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TickitLMS Learn.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **TickitLMS Learn**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-tickitlms-learn-sso"></a>Konfigurera TickitLMS Learn SSO

Om du vill konfigurera enkel inloggning på **sidan TickitLMS Learn** måste du skicka **url:en för App Federationsmetadata** till Stödteamet för [TickitLMS Learn](mailto:support@tickitlms.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-tickitlms-learn-test-user"></a>Skapa Testanvändare för TickitLMS Lär dig

I det här avsnittet skapar du en användare som heter Britta Simon i TickitLMS Learn. Arbeta med [Stödteamet för TickitLMS Lär](mailto:support@tickitlms.com) dig för att lägga till användarna i Plattformen TickitLMS Learn. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TickitLMS Learn på åtkomstpanelen bör du automatiskt loggas in på den TickitLMS Learn som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova TickitLMS Learn med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du TickitLMS Learn med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

