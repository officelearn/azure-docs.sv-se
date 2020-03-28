---
title: 'Självstudiekurs: Azure Active Directory-integrering med Shmoop for Schools | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Shmoop For Schools.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ce2e51f72b9b4f709bf26ce6d92ad3861d36db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74081645"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>Självstudiekurs: Integrera Shmoop för skolor med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Shmoop for Schools med Azure Active Directory (Azure AD). När du integrerar Shmoop For Schools med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Shmoop för skolor.
* Gör att användarna automatiskt loggas in på Shmoop For Schools med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Shmoop For Schools enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Shmoop For Schools stöder **SP** initierade SSO
* Shmoop For Schools stöder just in time-användaretablering **Just In Time**

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Lägga shmoop för skolor från galleriet

Om du vill konfigurera integreringen av Shmoop For Schools i Azure AD måste du lägga till Shmoop For Schools från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Shmoop för skolor** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Shmoop for Schools från** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-shmoop-for-schools"></a>Konfigurera och testa en enda Azure AD-inloggning för Shmoop för skolor

Konfigurera och testa Azure AD SSO med Shmoop For Schools med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Shmoop For Schools.

Om du vill konfigurera och testa Azure AD SSO med Shmoop For Schools slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera Shmoop For Schools SSO](#configure-shmoop-for-schools-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    * **[Skapa Shmoop For Schools testanvändare](#create-shmoop-for-schools-test-user)** – om du vill ha en motsvarighet till B.Simon i Shmoop for Schools som är länkad till Azure AD-representationen för användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Programintegrering** av [Azure-portalen](https://portal.azure.com/)och välj Enkel inloggning på sidan **Shmoop For** **Schools-programintegration**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Shmoop For Schools Client support team](mailto:support@shmoop.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Shmoop For Schools-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

    > [!NOTE]
    > Shmoop for School stöder två roller för användare: **Lärare** och **Elev**. Ställ in dessa roller i Azure AD så att användare kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Utöver ovanstående, Shmoop For Schools ansökan förväntar sig några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | --------- | --------------- |
    | roll      | user.assignedroles |

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Shmoop for Schools.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Shmoop för skolor**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-shmoop-for-schools-sso"></a>Konfigurera Shmoop för skolor SSO

Om du vill konfigurera enkel inloggning på **Shmoop For Schools-sidan** måste du skicka **appfederationens metadataadress** till [shmoop för skolors supportteam](mailto:support@shmoop.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-shmoop-for-schools-test-user"></a>Skapa Shmoop for Schools testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Shmoop For Schools. Shmoop For Schools stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Shmoop for Schools skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för Shmoop For Schools](mailto:support@shmoop.com).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Shmoop for Schools i åtkomstpanelen ska du automatiskt loggas in på den Shmoop för skolor som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Shmoop för skolor med Azure AD](https://aad.portal.azure.com/)