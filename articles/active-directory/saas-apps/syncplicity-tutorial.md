---
title: 'Självstudiekurs: Azure Active Directory-integrering med Syncplicity | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233289"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Självstudiekurs: Integrera synkronisering med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Syncplicity med Azure Active Directory (Azure AD). När du integrerar Syncplicity med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Syncplicity.
* Gör att användarna automatiskt loggas in i Synkronisering med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* En automatisk inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Syncplicity stöder **SP** initierade SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Lägga till Syncplicity från galleriet

Om du vill konfigurera integreringen av Syncplicity i Azure AD måste du lägga till Synkronisering från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Synkronisering** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Syncplicity** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

Konfigurera och testa Azure AD SSO med Syncplicity med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Syncplicity.

Om du vill konfigurera och testa Azure AD SSO med Syncplicity slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Syncplicity SSO](#configure-syncplicity-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Syncplicity-testanvändare](#create-syncplicity-test-user)** – om du vill ha en motsvarighet till B.Simon i Syncplicity som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Synkroniseringsprogram i** [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.syncplicity.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Syncplicity Client supportteam](https://www.syncplicity.com/contact-us) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera synkronisering.**

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurera Syncplicity SSO

1. Logga in på din **Syncplicity-klient.**

1. Klicka på **Admin**på menyn högst upp, välj **inställningar**och klicka sedan **på Anpassad domän och enkel inloggning**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Gör följande på dialogsidan **för Enkel inloggning (SSO):**

    ![Enkel inloggning \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Skriv namnet på domänen i textrutan **Anpassad** domän.
  
    b. Välj **Aktiverad** som **enkel inloggningsstatus**.

    c. I textrutan **Entitets-ID** klistrar du in värdet **Identifierare (Entitets-ID)** som du har använt i **den grundläggande SAML-konfigurationen** i Azure-portalen.

    d. Klistra in **inloggningssidans URL-textruta** genom att klistra in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    e. Klistra in **url-url-url:en som** du har kopierat från Azure-portalen i **url-textrutan** för utloggningssidan.

    f. I **Identity Provider Certificate**klickar du på Välj **fil**och överför sedan certifikatet som du har hämtat från Azure-portalen.

    g. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Syncplicity.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Syncplicity i**programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-syncplicity-test-user"></a>Skapa syncplicity testanvändare

För att Azure AD-användare ska kunna logga in måste de etableras i Syncplicity-programmet. I det här avsnittet beskrivs hur du skapar Azure AD-användarkonton i Syncplicity.

**Så här etablerar du ett användarkonto till Syncplicity:**

1. Logga in på din **Syncplicity-klient** (till exempel: `https://company.Syncplicity.com`).

1. Klicka på **admin** och välj **användarkonton** och klicka sedan på **LÄGG TILL EN ANVÄNDARE**.

    ![Hantera användare](./media/syncplicity-tutorial/ic769764.png "Hantera användare")

1. Skriv **e-postadresserna** till ett Azure AD-konto som du vill etablera, välj **Användare** som **roll**och klicka sedan på **NÄSTA**.

    ![Kontoinformation](./media/syncplicity-tutorial/ic769765.png "Kontoinformation")

    > [!NOTE]
    > Azure AD-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta och aktivera kontot.

1. Välj en grupp i företaget som den nya användaren ska bli medlem i och klicka sedan på **NÄSTA**.

    ![Gruppmedlemskap](./media/syncplicity-tutorial/ic769772.png "Gruppmedlemskap")

    > [!NOTE]
    > Om det inte finns några grupper i listan klickar du på **NÄSTA**.

1. Markera de mappar som du vill placera under Syncplicitys kontroll på användarens dator och klicka sedan på **NÄSTA**.

    ![Synkronisera mappar](./media/syncplicity-tutorial/ic769773.png "Synkronisera mappar")

> [!NOTE]
> Du kan använda andra verktyg för att skapa synkroniseringsanvändarkonton eller API:er som tillhandahålls av Syncplicity för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Syncplicity på åtkomstpanelen bör du automatiskt loggas in i den synkronisering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)