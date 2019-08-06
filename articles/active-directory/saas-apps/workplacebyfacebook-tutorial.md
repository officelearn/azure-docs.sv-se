---
title: 'Självstudier: Azure Active Directory-integrering med Workplace by Facebook | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4328c6b5b85050ae5caf30fd4d321e50428f10b9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825395"
---
# <a name="tutorial-integrate-workplace-by-facebook-with-azure-active-directory"></a>Självstudier: Integrera arbets ytan på Facebook med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar arbets ytan i Facebook med Azure Active Directory (Azure AD). När du integrerar arbets ytan av Facebook med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till arbets platsen av Facebook.
* Gör det möjligt för användarna att logga in automatiskt på arbets platsen av Facebook med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration på arbets plats av Facebook-enkel inloggning (SSO).

> [!NOTE]
> Facebook har två produkter, Workplace Standard (kostnadsfri) och Workplace Premium (betalprodukt). Alla Workplace Premium-klientorganisationer kan konfigurera integrering med SCIM och enkel inloggning utan att det påverkar kostnaden eller de licenser som behövs. Enkel inloggning och SCIM är inte tillgängliga i Workplace Standard-instanser.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Workplace by Facebook har stöd för **SP**-initierad enkel inloggning
* Workplace by Facebook har stöd för **just-in-time-etablering**
* Workplace by Facebook har stöd för **[automatisk användarförsörjning](workplacebyfacebook-provisioning-tutorial.md)**
* Arbets platsen av Facebook Mobile Application kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Lägga till Workplace by Facebook från galleriet

För att konfigurera integrering av Workplace by Facebook med Azure AD behöver du lägga till Workplace by Facebook från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **arbets yta efter Facebook** i sökrutan.
1. Välj **arbets plats per Facebook** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med arbets ytan på Facebook med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på arbets ytan av Facebook.

Om du vill konfigurera och testa Azure AD SSO med arbets ytan på Facebook slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera arbets ytan efter Facebook SSO](#configure-workplace-by-facebook-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa arbets plats efter Facebook](#create-workplace-by-facebook-test-user)** -testanvändare – om du vill ha en motsvarighet till B. Simon på arbets ytan av Facebook som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **arbets plats av Facebook** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instancename>.facebook.com`

    b. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > De här värdena är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Korrekta värden för din Workplace-community finns på sidan Autentisering på företagets instrumentpanel för Workplace.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. På sidan **Konfigurera arbets plats efter Facebook** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-workplace-by-facebook-sso"></a>Konfigurera arbets yta efter Facebook SSO

1. I ett annat webbläsarfönster loggar du in på din Workplace by Facebook-företagsplats som administratör.
  
    > [!NOTE]
    > Som en del av SAML-autentiseringsprocessen kan Workplace by Facebook utnyttja frågesträngar på upp till 2,5 kB i storlek för att skicka parametrar till Azure AD.

2. I **administrationspanelen** går du till fliken **Säkerhet**.

    ![Administrationspanel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. Under fliken **Autentisering** väljer du **Enkel inloggning** och utför följande steg:

    ![Fliken Autentisering](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. I textrutan **SAML URL** klistrar du in det värde för **Login URL** (Inloggnings-URL) som du har kopierat från Azure-portalen.

    b. I **text rutan URI för SAML**-utfärdare klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. I **SAML Logout Redirect** (Omdirigering för SAML-utloggning) (valfritt) klistrar du in det värde för **Utloggnings-URL** som du har kopierat från Azure-portalen.

    d. Öppna ditt **base-64-kodade certifikat** som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet i Urklipp och klistra sedan in den i textrutan **SAML Certificate** (SAML-certifikat).

    e. Kopiera **mål webb adressen** för din instans och klistra in den i text rutan **identifierare (entitets-ID)** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    f. Kopiera **mottagarens URL** för din instans och klistra in den i text rutan för inloggnings- **URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    g. Rulla ned till slutet av avsnittet och klicka på knappen **Test SSO** (Testa enkel inloggning). Då visas ett popup-fönster med Azure AD-inloggningssidan. Ange dina autentiseringsuppgifter som vanligt för att autentisera.

    **Felsökning:** Kontrollera att den e-postadress som returneras från Azure AD är samma som det Workplace-konto som du har loggat in med.

    h. När testet är klart rullar du längst ned på sidan och klickar på knappen **Spara**.

    i. Alla användare som använder Workplace ser nu Azure AD-inloggningssidan för autentisering.

4. **SAML Logout Redirect** - (Omdirigering för SAML-utloggning) (valfritt)

    Du kan även välja att konfigurera en SAML-utloggnings-URL som kan användas för att peka på utloggningssidan för Azure AD. När den här inställningen aktiveras och konfigureras omdirigeras användarna inte längre till utloggningssidan för Workplace. I stället omdirigeras de till den URL som lades till i inställningen för SAML-utloggningsomdirigering.

### <a name="configuring-reauthentication-frequency"></a>Konfigurera omautentiseringsfrekvens

Du kan konfigurera Workplace att fråga efter en SAML-kontroll varje dag, var tredje dag, varje vecka, varannan vecka, varje månad eller aldrig.

> [!NOTE]
> Minimivärdet för SAML-kontrollen i mobilprogram är inställt på en vecka.

Du kan även tvinga en SAML-återställning för alla användare med knappen: Kräv SAML-autentisering för alla användare nu.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets platsen av Facebook.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **arbets plats efter Facebook**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workplace-by-facebook-test-user"></a>Skapa Workplace by Facebook-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i arbets ytan av Facebook. Workplace by Facebook har stöd för just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd för dig i det här avsnittet. Om det inte finns någon användare i Workplace by Facebook skapas en ny när du försöker få åtkomst till Workplace by Facebook.

>[!Note]
>Kontakta [Workplace by Facebook-kundsupporten](https://workplace.fb.com/faq/) om du behöver skapa en användare manuellt

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Workplace by Facebook-panelen i åtkomstpanelen bör du automatiskt loggas in på Workplace by Facebook som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testa SSO för arbets plats av Facebook (mobil)

1. Öppna arbets plats efter Facebook Mobile-program. På inloggnings sidan klickar du på **Logga**in.

    ![Inloggningen](./media/workplacebyfacebook-tutorial/test05.png)

2. Ange ditt företags-e-postmeddelande och klicka på **Fortsätt**.

    ![E-postmeddelandet](./media/workplacebyfacebook-tutorial/test02.png)

3. Klicka på **bara en gång**.

    ![En gång](./media/workplacebyfacebook-tutorial/test04.png)

4. Klicka på **Tillåt**.

    ![Alternativet Tillåt](./media/workplacebyfacebook-tutorial/test03.png)

5. När du har loggat in igen visas programmets start sida.    

    ![Start Sidan](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](workplacebyfacebook-provisioning-tutorial.md)

