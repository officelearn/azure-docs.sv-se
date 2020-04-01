---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Velpic SAML | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72241565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Velpic SAML

I den här självstudien får du lära dig hur du integrerar Velpic SAML med Azure Active Directory (Azure AD). När du integrerar Velpic SAML med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Velpic SAML.
* Gör att användarna automatiskt loggas in på Velpic SAML med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Velpic SAML enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Velpic SAML stöder **SP** initierade SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Lägga Velpic SAML från galleriet

Om du vill konfigurera integreringen av Velpic SAML i Azure AD måste du lägga till Velpic SAML från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Velpic SAML** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Velpic SAML** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Konfigurera och testa en azure AD-inloggning för Velpic SAML

Konfigurera och testa Azure AD SSO med Velpic SAML med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Velpic SAML.

Så här konfigurerar och testar du Azure AD SSO med Velpic SAML:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Velpic SAML SSO](#configure-velpic-saml-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Velpic SAML-testanvändare](#create-velpic-saml-test-user)** – om du vill ha en motsvarighet till B.Simon i Velpic SAML som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<sub-domain>.velpicsaml.net`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Observera att inloggningsadressen kommer att tillhandahållas av Velpic SAML-teamet och identifierare värdet kommer att vara tillgänglig när du konfigurerar SSO Plugin på Velpic SAML-sidan. Du måste kopiera det värdet från Velpic SAML ansökan sida och klistra in den här.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser på avsnittet **Konfigurera Velpic SAML** baserat på dina behov.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Velpic SAML.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Velpic SAML**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-velpic-saml-sso"></a>Konfigurera Velpic SAML SSO

1. Om du vill automatisera konfigurationen i Velpic SAML måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till förlängning i webbläsaren, klicka på **Ställ in Velpic SAML** kommer att leda dig till Velpic SAML ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Velpic SAML. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-8.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Velpic SAML manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Velpic SAML-företagswebbplats som administratör och utför följande steg:

4. Klicka på **Hantera** fliken och gå till **integration** avsnitt där du måste klicka på **Plugins** knappen för att skapa nya plugin för inloggning.

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_1.png)

5. Klicka på **knappen "Lägg till plugin".**
    
    ![Plugin-program](./media/velpicsaml-tutorial/velpic_2.png)

6. Klicka på **SAML-panelen** på sidan Lägg till plugin.
    
    ![Plugin-program](./media/velpicsaml-tutorial/velpic_3.png)

7. Ange namnet på den nya SAML plugin och klicka på **"Lägg till"-knappen.**

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_4.png)

8. Ange informationen enligt följande:

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_5.png)

    a. Skriv namnet på SIML-plugin i textrutan **Namn.**

    b. I textrutan **Utfärdare URL** klistrar du in **Den Azure AD-identifierare** som du kopierade från **fönstret Konfigurera inloggning på Azure-portalen.**

    c. I **Provider Metadata Config** ladda upp metadata XML-filen som du hämtade från Azure-portalen.

    d. Du kan också välja att aktivera SAML precis i tid etablering genom att aktivera **"Skapa automatiskt nya användare"** kryssrutan. Om en användare inte finns i Velpic och den här flaggan inte är aktiverad misslyckas inloggningen från Azure. Om flaggan är aktiverad kommer användaren automatiskt att etableras i Velpic vid tidpunkten för inloggningen. 

    e. Kopiera **url:en enkel inloggning** från textrutan och klistra in den i Azure-portalen.
    
    f. Klicka på **Spara**.

### <a name="create-velpic-saml-test-user"></a>Skapa Velpic SAML-testanvändare

Det här steget krävs vanligtvis inte eftersom programmet stöder just i tid användaretablering. Om den automatiska användaretablering inte är aktiverad kan manuellt användarskapande göras enligt beskrivningen nedan.

Logga in på din Velpic SAML-företagswebbplats som administratör och utför följande steg:
    
1. Klicka på Fliken Hantera och gå till avsnittet Användare och klicka sedan på Knappen Nytt för att lägga till användare.

    ![lägga till användare](./media/velpicsaml-tutorial/velpic_7.png)

2. Gör följande på dialogsidan **"Skapa ny användare".**

    ![användare](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Skriv förnamnet B i textrutan **Förnamn.**

    b. Skriv efternamnet på Simon i textrutan **Efternamn.**

    c. Skriv användarnamnet för B.Simon i textrutan **Användarnamn.**

    d. Skriv kontots B.Simon@contoso.com e-postadress i **textrutan e-post.**

    e. Resten av informationen är valfri kan du fylla i den om det behövs.
    
    f. Klicka på **SPARA**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

1. När du klickar på Velpic SAML-panelen i åtkomstpanelen bör du få inloggningssidan för Velpic SAML-programmet. Du bör se knappen **Logga in med Azure AD** på inloggningssidan.

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_6.png)

1. Klicka på knappen **Logga in med Azure AD** för att logga in på Velpic med ditt Azure AD-konto.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Velpic SAML med Azure AD](https://aad.portal.azure.com/)

