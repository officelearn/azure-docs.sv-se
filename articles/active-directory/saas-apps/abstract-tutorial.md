---
title: 'Självstudiekurs: Azure Active Directory-integrering med sammanfattning | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Abstract.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68302592"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Självstudiekurs: Integrera abstract med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Abstract med Azure Active Directory (Azure AD). När du integrerar Abstract med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Abstract.
* Aktivera dina användare så att de automatiskt loggas in på Abstract med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Abstrakt enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Abstract stöder **SP och IDP** initierade SSO

## <a name="adding-abstract-from-the-gallery"></a>Lägga till abstract från galleriet

Om du vill konfigurera integreringen av Abstract i Azure AD måste du lägga till Abstract från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Sammanfattning** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Sammanfattning** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Abstract med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Abstract.

Om du vill konfigurera och testa Azure AD SSO med Abstract slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera abstrakt SSO](#configure-abstract-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa abstrakt testanvändare](#create-abstract-test-user)** - om du vill ha en motsvarighet till Britta Simon i Abstract som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**. **Abstract**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP-initierat** läge och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://app.abstract.com/signin`

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurera abstrakt SSO

Se till att `App Federation Metadata Url` hämta `Azure AD Identifier` din och från Azure-portalen, eftersom du behöver dem för att konfigurera SSO på Abstract.

Du hittar dessa uppgifter på sidan **Konfigurera enkel inloggning med SAML:**

* Den `App Federation Metadata Url` finns i avsnittet **SAML Signering Certificate.**
* Den `Azure AD Identifier` finns i avsnittet **Konfigurera sammanfattning.**


Du är nu redo att konfigurera SSO på Abstract:

>[!Note]
>Du måste autentisera med ett organisationsadministratörskonto för att komma åt SSO-inställningarna på Abstract.

1. Öppna den [abstrakta webbappen](https://app.abstract.com/).
2. Gå till sidan **Behörigheter** i det vänstra sidofältet.
3. I avsnittet **Konfigurera SSO** anger du **url:en och** **enhets-ID:t**för metadata .
4. Ange eventuella manuella undantag som du kan ha. E-postmeddelanden som anges i avsnittet med manuella undantag kommer att kringgå SSO och kunna logga in med e-post och lösenord. 
5. Klicka på **Spara ändringar**.

>[!Note] 
>Du måste använda primära e-postadresser i listan över manuella undantag. SSO-aktiveringen misslyckas om e-postmeddelandet du listar är en användares sekundära e-post. Om det händer visas ett felmeddelande med det primära e-postmeddelandet för det felaktiga kontot. Lägg till den primära e-postmeddelandet i listan över manuella undantag när du har verifierat att du känner användaren.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Abstract.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Sammanfattning**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-abstract-test-user"></a>Skapa abstrakt testanvändare

Så här testar du SSO på Abstract:

1. Öppna den [abstrakta webbappen](https://app.abstract.com/).
2. Gå till sidan **Behörigheter** i det vänstra sidofältet.
3. Klicka på **Testa med mitt konto**. Om testet misslyckas, vänligen [kontakta vårt supportteam.](https://www.abstract.com/help/contact/)

>[!Note]
>Du måste autentisera med ett organisationsadministratörskonto för att komma åt SSO-inställningarna på Abstract.
Det här organisationsadministratörskontot måste tilldelas Abstract på Azure-portalen.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Sammanfattning på åtkomstpanelen bör du automatiskt loggas in på den sammanfattning som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

