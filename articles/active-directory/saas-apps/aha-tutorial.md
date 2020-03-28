---
title: 'Självstudiekurs: Azure Active Directory-integrering med Aha! | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a63872680d28664c6d5a7ff109f6de72817173d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68989652"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Handledning: Integrera Aha! med Azure Active Directory

I den här guiden får du lära dig att integrera Aha! med Azure Active Directory (Azure AD). När du integrerar Aha! med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Aha!.
* Gör det möjligt för användarna att automatiskt logga in på Aha! med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Aha!-prenumeration enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Aha!-prenumeration stöder **SP**-initierad enkel inloggning
* Aha!-prenumeration stöder **just-in-time**-användaretablering

## <a name="adding-aha-from-the-gallery"></a>Lägga till Aha! från galleriet

För att konfigurera integrering av Aha! med Azure AD behöver du lägga till Aha! från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Aha** i avsnittet Lägg till från **galleriet!** i sökfältet.
1. Välj **Aha!** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Konfigurera och testa azure AD enkel inloggning för Aha!

Konfigurera och testa Azure AD SSO med Aha! med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Aha!.

Om du vill konfigurera och testa Azure AD SSO med Aha!slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera Aha! SSO](#configure-aha-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Aha! testanvändare](#create-aha-test-user)** - att ha en motsvarighet till B.Simon i Aha! som är länkad till en Azure AD-representation av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för **Aha!**-programintegrering sidan Programintegrering, leta reda på avsnittet **Hantera** och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.aha.io/session/new`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.aha.io`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Aha! Klientsupportteam](https://www.aha.io/company/contact) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Aha!** kopiera lämpliga webbadresser baserat på dina krav.

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Aha!.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Aha!**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-aha-sso"></a>Konfigurera enkel inloggning Enkel inloggning

1. Om du vill automatisera konfigurationen i Aha!, måste du installera **Webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till förlängning i webbläsaren, klicka på **Setup Aha!** kommer att leda dig till Aha! Program. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Aha!. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-8.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill ställa in Aha! manuellt, öppna ett nytt webbläsarfönster och logga in på din Aha! företagets webbplats som administratör och utföra följande steg:

4. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/aha-tutorial/IC798950.png "Inställningar")

5. Klicka på **Konto**.
  
    ![Profil](./media/aha-tutorial/IC798951.png "Profil")

6. Klicka på **Säkerhet och enkel inloggning**.

    ![Säkerhet och enkel inloggning](./media/aha-tutorial/IC798952.png "Säkerhet och enkel inloggning")

7. I avsnittet **Enkel inloggning** anger du **Identitetsprovider** till **SAML2.0**.

    ![Säkerhet och enkel inloggning](./media/aha-tutorial/IC798953.png "Säkerhet och enkel inloggning")

8. På sidan för konfiguration av **Enkel inloggning** utför du följande steg:

    ![Enkel inloggning](./media/aha-tutorial/IC798954.png "för Aha!")

    a. I textrutan **Namn** skriver du ett namn för konfigurationen.

    b. För **Configure using** (Konfigurera med hjälp av) väljer du **Metadata File** (Metadatafil).

    c. För att ladda upp den nedladdade metadatafilen klickar du på **Bläddra**.

    d. Klicka på **Uppdatera**.

### <a name="create-aha-test-user"></a>Skapa testanvändare för Aha!

I det här avsnittet skapas en användare som heter B.Simon i Aha!. Aha!-prenumeration stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Aha! skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Aha! i åtkomstpanelen bör du automatiskt loggas in på Aha! som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

