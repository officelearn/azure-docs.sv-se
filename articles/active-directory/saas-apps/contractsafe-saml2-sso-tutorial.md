---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med ContractSafe Saml2 SSO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ContractSafe Saml2 SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77185622"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Självstudiekurs: Integrera Azure Active Directory enkel inloggning (SSO) med ContractSafe Saml2 SSO

I den här självstudien får du lära dig hur du integrerar ContractSafe Saml2 SSO med Azure Active Directory (Azure AD). När du integrerar ContractSafe Saml2 SSO med Azure AD kan du:

* Styra vem som har åtkomst till ContractSafe Saml2 SSO i Azure AD.
* Gör det möjligt för användarna att automatiskt logga in på ContractSafe Saml2 SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En ContractSafe Saml2 SSO-prenumeration med SSO aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. ContractSafe Saml2 SSO stöder **IDP-initierad**SSO.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Lägg till ContractSafe Saml2 SSO från galleriet

Om du vill konfigurera integreringen av ContractSafe Saml2 SSO i Azure AD måste du lägga till ContractSafe Saml2 SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **ContractSafe Saml2 SSO** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **ContractSafe Saml2 SSO** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Konfigurera och testa Azure AD SSO för ContractSafe Saml2 SSO

Konfigurera och testa Azure AD SSO med ContractSafe Saml2 SSO med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i ContractSafe Saml2 SSO.

Så här konfigurerar och testar du Azure AD SSO med ContractSafe Saml2 SSO:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
   * [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med hjälp av **B.Simon-kontot.**
   * [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera **B.Simon** för att använda Azure AD SSO.

1. [Konfigurera ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) för att konfigurera SSO-inställningarna på programsidan.
   * [Skapa en ContractSafe Saml2 SSO-testanvändare](#create-a-contractsafe-saml2-sso-test-user) för att ha en motsvarighet till **B.Simon** i ContractSafe Saml2 SSO som är länkad till Azure AD-representationen av användaren.
2. [Testa SSO](#test-sso) för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Så här aktiverar du Azure AD SSO i Azure-portalen:

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet Hantera på sidan **ContractSafe Saml2** SSO-programintegration och väljer **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du redigeringsikonen (penn)för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du följande värden i motsvarande fält:

    a. Ange en URL i textrutan **Identifierare** med hjälp av följande format:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Ange en URL i textrutan **Svara** URL med följande format:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Kontakta [Supportteamet för ContractSafe Saml2 SSO Client](mailto:support@contractsafe.com) för att få dessa värden. Du kan också referera till de format som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. ContractSafe Saml2 SSO förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![Vanliga standardattribut](common/default-attributes.png)

1. Förutom standardattributen förväntar sig ContractSafe Saml2 SSO-programmet att några fler attribut skickas tillbaka i SAML-svaret. Dessa attribut är förifyllda, men du kan granska dem enligt dina krav. I följande lista visas ytterligare attribut.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | e-postnamn | user.userprincipalname |
    | e-post | user.onpremisesuserprincipalnamn |

1. Leta reda på XML för **federationsmetadata**i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML.** Välj **Hämta** om du vill hämta certifikatet och spara det sedan på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera ContractSafe Saml2 SSO** kopierar du lämpliga webbadresser baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter **B.Simon**.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen**. Välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange en e-postadress i `username@companydomain.extension` formatet i fältet **Användarnamn.** Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Välj **Skapa**.

## <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera **B.Simon** för att använda Azure SSO genom att bevilja åtkomst till ContractSafe Saml2 SSO.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **ContractSafe Saml2 SSO**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer sedan **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

   ![Länken Lägg till användare](common/add-assign-user.png)

1. Välj **B.Simon** i listan **Användare** **och grupper.** Välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **knappen Välj** längst ned på skärmen.
1. Välj knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-contractsafe-saml2-sso"></a>Konfigurera ContractSafe Saml2 SSO

Om du vill konfigurera SSO på **ContractSafe Saml2 SSO-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till Supportteamet för [ContractSafe Saml2 SSO](mailto:support@contractsafe.com). Teamet ansvarar för att ställa in SAML SSO-anslutningen korrekt på båda sidor.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Skapa en ContractSafe Saml2 SSO-testanvändare

Skapa en användare som heter B.Simon i ContractSafe Saml2 SSO. Arbeta med Supportteamet för [ContractSafe Saml2 SSO](mailto:support@contractsafe.com) för att lägga till användarna i ContractSafe Saml2 SSO-plattformen. Användare måste skapas och aktiveras innan du använder SSO.

## <a name="test-sso"></a>Testa SSO

Testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen. När du väljer panelen ContractSafe Saml2 SSO på åtkomstpanelen ska du automatiskt loggas in på den ContractSafe Saml2 SSO som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ContractSafe Saml2 SSO med Azure AD](https://aad.portal.azure.com/)
