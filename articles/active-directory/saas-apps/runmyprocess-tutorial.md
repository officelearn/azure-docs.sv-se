---
title: 'Självstudiekurs: Azure Active Directory-integrering med RunMyProcess | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880378"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Självstudiekurs: Integrera RunMyProcess med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar RunMyProcess med Azure Active Directory (Azure AD). När du integrerar RunMyProcess med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RunMyProcess.
* Aktivera dina användare så att de automatiskt loggas in på RunMyProcess med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* RunMyProcess enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* RunMyProcess **SP** stöder SP-initierad SSO

## <a name="adding-runmyprocess-from-the-gallery"></a>Lägga till RunMyProcess från galleriet

Om du vill konfigurera integreringen av RunMyProcess i Azure AD måste du lägga till RunMyProcess från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **RunMyProcess** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **RunMyProcess** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med RunMyProcess med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i RunMyProcess.

Så här konfigurerar och testar du Azure AD SSO med RunMyProcess:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RunMyProcess SSO](#configure-runmyprocess-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa RunMyProcess-testanvändare](#create-runmyprocess-test-user)** – om du vill ha en motsvarighet till B.Simon i RunMyProcess som är länkad till Azure AD-representationen för användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **KörMyProcess-program** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta Supportteamet för [RunMyProcess-klienten](mailto:support@runmyprocess.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera RunMyProcess** baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Konfigurera RunMyProcess SSO

1. I ett annat webbläsarfönster loggar du in på din RunMyProcess-klientorganisation som administratör.

1. I den vänstra navigeringspanelen klickar du på **Konto** och väljer **Konfiguration**.

    ![Konfigurera enkel inloggning på appsidan](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Gå till avsnittet **Autentiseringsmetod** och utför nedanstående steg:

    ![Konfigurera enkel inloggning på appsidan](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Som **metod**väljer du **SSO med Samlv2**.

    b. Klistra in värdet **för inloggnings-URL**i **textrutan SSO-omdirigering** , som du har kopierat från Azure-portalen.

    c. Klistra in värdet för url för **utloggning**i textrutan **Logout omdirigering** , som du har kopierat från Azure-portalen.

    d. I textrutan **Namn-ID-format** skriver du värdet **för namnidentifierarformat** som **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    e. Öppna den nedladdade certifikatfilen från Azure-portalen i anteckningar, kopiera innehållet i certifikatfilen och klistra sedan in den i **textrutan för certifikat.**

    f. Klicka på **Ikonen Spara.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RunMyProcess.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **RunMyProcess**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-runmyprocess-test-user"></a>Skapa RunMyProcess-testanvändare

För att Azure AD-användare ska kunna logga in på RunMyProcess måste de etableras i RunMyProcess. När det gäller RunMyProcess är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din RunMyProcess-företagswebbplats som administratör.

1. Klicka på **Konto** och välj **Användare** i den vänstra navigeringspanelen och klicka sedan på **Ny användare**.

    ![Ny användare](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Ny användare")

1. Gör följande i avsnittet **Användarinställningar:**

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Skriv **namn** och e-post för ett giltigt Azure **AD-konto** som du vill etablera i relaterade textrutor.

    b. Välj ett **IDE-språk,** **språk**och **profil**.

    c. Välj **Skicka e-post för att skapa konton till mig**.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Du kan använda andra Verktyg eller API:er för att skapa runmyprocess-användarkonton som tillhandahålls av RunMyProcess för att etablera Azure Active Directory-användarkonton.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen RunMyProcess på åtkomstpanelen bör du automatiskt loggas in på den RunMyProcess som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)