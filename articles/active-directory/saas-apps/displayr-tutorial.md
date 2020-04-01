---
title: 'Självstudiekurs: Azure Active Directory-integrering med Displayr | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103989"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Självstudiekurs: Integrera Displayr med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Displayr med Azure Active Directory (Azure AD). När du integrerar Displayr med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Displayr.
* Gör att användarna automatiskt loggas in på Displayr med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Automatisk inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Displayr stöder **SP** initierade SSO.

## <a name="adding-displayr-from-the-gallery"></a>Lägga till Displayr från galleriet

Om du vill konfigurera integreringen av Displayr i Azure AD måste du lägga till Displayr från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Displayr** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Displayr** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Displayr med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Displayr.

Om du vill konfigurera och testa Azure AD SSO med Displayr slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Displayr](#configure-displayr)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** så att Britta Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Displayr-testanvändare](#create-displayr-test-user)** för att ha en motsvarighet till Britta Simon i Displayr som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Displayr** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YOURDOMAIN>.displayr.com`

    b. I textrutan **Identifierare (Enhetsenhets-ID)** skriver du en URL med följande mönster:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Displayr Client supportteam](mailto:support@displayr.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet Grundläggande SAML-konfiguration i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Displayr-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig Displayr-programmet att få fler attribut skickas tillbaka i SAML-svar. Gör följande i avsnittet **Användarattribut & anspråk** i dialogrutan **Gruppanspråk (förhandsversion):**

    a. Klicka på **pennan** **bredvid Grupper som returneras i anspråk**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Välj **Alla grupper** i radiolistan.

    c. Välj **källattribut för** **grupp-ID**.

    d. Markera **Anpassa namnet på gruppanspråket**.

    e. Kontrollera **Emit-grupper som rollanspråk**.

    f. Klicka på **Spara**.

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera displayer** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Konfigurera displayr

1. Om du vill automatisera konfigurationen i Displayr måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Setup Displayr** som leder dig till Displayr-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Displayr. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Displayr manuellt öppnar du ett nytt webbläsarfönster och loggar in på Displayr-företagets webbplats som administratör och utför följande steg:

4. Klicka på **Inställningar** och navigera till **Konto**.

    ![Konfiguration](./media/displayr-tutorial/config01.png)

5. Växla till **Inställningar** från den övre menyn och bläddra nedåt på sidan för att klicka på **Konfigurera SAML (Configure Single Sign On).**

    ![Konfiguration](./media/displayr-tutorial/config02.png)

6. Gör följande på sidan **Saml (Single Sign On):**

    ![Konfiguration](./media/displayr-tutorial/config03.png)

    a. Markera rutan **Aktivera siml (Enable Single Sign On).**

    b. Kopiera det faktiska **identifierarevärdet** från avsnittet **Grundläggande SAML-konfiguration** i Azure AD och klistra in det i textrutan **Utfärdare.**

    c. Klistra in värdet **för inloggnings-URL**i textrutan **Inloggnings-URL** som du har kopierat från Azure-portalen.

    d. Klistra in värdet för url för **utloggning**som du har kopierat från Azure-portalen i textrutan **Utloggning.**

    e. Öppna certifikatet (Base64) i Anteckningar, kopiera innehållet och klistra in det i textrutan **Certifikat.**

    f. **Gruppmappningar** är valfria.

    g. Klicka på **Spara**.  

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Displayr.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Displayr**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-displayr-test-user"></a>Skapa testanvändare för Displayr

Om du vill aktivera Azure AD-användare loggar du in på Displayr, de måste etableras i Displayr. I Displayr är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Displayr som administratör.

2. Klicka på **Inställningar** och navigera till **Konto**.

    ![Konfiguration av displayr](./media/displayr-tutorial/config01.png)

3. Växla till **Inställningar** från den övre menyn och bläddra nedåt på sidan, tills **avsnittet Användare** klickar sedan på **Ny användare**.

    ![Konfiguration av displayr](./media/displayr-tutorial/config07.png)

4. Gör följande på sidan **Ny användare:**

    ![Konfiguration av displayr](./media/displayr-tutorial/config06.png)

    a. I textrutan **Namn** anger du namnet på användaren som **Brittasimon**.

    b. I textrutan **E-post** anger du användarens e-postadress som `Brittasimon@contoso.com`.

    c. Välj lämpligt **gruppmedlemskap**.

    d. Klicka på **Spara**.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Displayr på åtkomstpanelen ska du automatiskt loggas in på den displayer som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
