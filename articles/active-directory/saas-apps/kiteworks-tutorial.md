---
title: 'Självstudiekurs: Azure Active Directory-integrering med Kiteworks | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kiteworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 52b113d92fa83795e94d5179ea47ed5d9d9e9a26
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68248905"
---
# <a name="tutorial-integrate-kiteworks-with-azure-active-directory"></a>Självstudiekurs: Integrera Kiteworks med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Kiteworks med Azure Active Directory (Azure AD). När du integrerar Kiteworks med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Kiteworks.
* Gör att användarna automatiskt loggas in i Kiteworks med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Kiteworks enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Kiteworks stöder **SP** initierade SSO
* Kiteworks stöder just in time-användaretablering **Just In Time**

## <a name="adding-kiteworks-from-the-gallery"></a>Lägga till Kiteworks från galleriet

Om du vill konfigurera integreringen av Kiteworks i Azure AD måste du lägga till Kiteworks från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Kiteworks** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Kiteworks** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Kiteworks med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Kiteworks.

Om du vill konfigurera och testa Azure AD SSO med Kiteworks slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Kiteworks SSO](#configure-kiteworks-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Kiteworks-testanvändare](#create-kiteworks-test-user)** – om du vill ha en motsvarighet till Britta Simon i Kiteworks som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<kiteworksURL>.kiteworks.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<kiteworksURL>/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Kiteworks Client supportteam](https://accellion.com/support) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Kiteworks** baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-kiteworks-sso"></a>Konfigurera Kiteworks SSO

1. Logga in på din Kiteworks företagswebbplats som administratör.

1. I verktygsfältet längst upp klickar du på **Inställningar**.

    ![Konfigurera enkel inloggning](./media/kiteworks-tutorial/tutorial_kiteworks_06.png)

1. Klicka på **SSO-installation i**avsnittet **Autentisering och auktorisering** .

    ![Konfigurera enkel inloggning](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)

1. Gör följande på sidan SSO-installationsprogrammet:

    ![Konfigurera enkel inloggning](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)

    a. Välj **Autentisera via SSO**.

    b. Välj **Initiera AuthnRequest**.

    c. Klistra in värdet för **Azure AD-identifierare**i **textrutan IDP-entitets-ID** , som du har kopierat från Azure-portalen.

    d. Klistra in värdet för **inloggnings-URL**i **textrutan För enkel inloggningstjänst** som du har kopierat från Azure-portalen.

    e. Klistra in värdet **för url för utloggning**i textrutan Url för **enkel utloggningstjänst** som du har kopierat från Azure-portalen.

    f. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet och klistra sedan in det i textrutan **RSA Public Key Certificate.**

    g. Klicka på **Spara**.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kiteworks.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Kiteworks**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-kiteworks-test-user"></a>Skapa Kiteworks-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i Kiteworks.

Kiteworks stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas under ett försök att komma åt Kiteworks om det inte finns ännu.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Kiteworks supportteam](https://accellion.com/support).

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på kiteworks-panelen på åtkomstpanelen ska du automatiskt loggas in på de Kiteworks som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
