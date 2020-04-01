---
title: 'Självstudiekurs: Integrering av Enkel inloggning i Azure Active Directory med personer | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och People.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a9b8f08a54c978d81a8d33c61ab3d5f5fc7271f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70164222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med personer

I den här självstudien får du lära dig hur du integrerar personer med Azure Active Directory (Azure AD). När du integrerar Personer med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till personer.
* Gör att användarna automatiskt loggas in på personer med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Personer enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Personer stöder **SP** initierade SSO
* People Mobile-programmet kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

>[!NOTE]
>Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-people-from-the-gallery"></a>Lägga till personer från galleriet

Om du vill konfigurera integreringen av Personer i Azure AD måste du lägga till personer från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Personer** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Personer** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-people"></a>Konfigurera och testa en enda Azure AD-inloggning för personer

Konfigurera och testa Azure AD SSO med personer som använder en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Kontakter.

Så här konfigurerar och testar du Azure AD SSO med Personer:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera Personer SSO](#configure-people-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa personer testanvändare](#create-people-test-user)** - att ha en motsvarighet till B.Simon i Kontakter som är kopplad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På sidan Integrering av **personprogram** på sidan Programintegrering i [Azure](https://portal.azure.com/)hittar du avsnittet **Hantera** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<company name>.peoplehr.net`

    b. I textrutan **Identifierare** skriver du in en URL: `https://www.peoplehr.com`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [Personer Client supportteam](mailto:customerservices@peoplehr.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser i avsnittet **Konfigurera personer** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Personer.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Personer**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-people-sso"></a>Konfigurera personer SSO

1. Om du vill automatisera konfigurationen i Kontakter måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Konfigurera personer** som leder dig till programmet Kontakter. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Kontakter. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Personer manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen Kontakter som administratör och utför följande steg:
   
4. Klicka på **Inställningar**på menyn till vänster .

    ![Konfigurera enkel inloggning](./media/people-tutorial/tutorial_people_001.png)

5. Klicka på **Företag**.

    ![Konfigurera enkel inloggning](./media/people-tutorial/tutorial_people_002.png)

6. Klicka på **Bläddra** för att ladda upp den nedladdade metadatafilen i **saml-metadatafilen För**att ladda upp sam-data för att ladda upp sam-data för att ladda upp den hämtade metadatafilen.

    ![Konfigurera enkel inloggning](./media/people-tutorial/tutorial_people_003.png)

### <a name="create-people-test-user"></a>Testa användare av skapa personer

I det här avsnittet skapar du en användare som heter B.Simon i Kontakter. Arbeta med [People Client support team](mailto:customerservices@peoplehr.com) för att lägga till användarna i people-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kontakter på åtkomstpanelen bör du automatiskt loggas in på den person som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-people-mobile"></a>Testa SSO för personer (mobil)

1. Öppna programmet Kontakter. På inloggningssidan anger du **e-post-ID:t** och klickar sedan på **Enkel inloggning**.

    ![Inloggningen](./media/people-tutorial/test01.png)

2. Ange **organisationen UserID** och klicka på **Nästa**.

    ![E-postmeddelandet](./media/people-tutorial/test02.png)

3. Slutligen efter lyckad inloggning, programmets hemsida kommer att visas nedan:

    ![Den en gång](./media/people-tutorial/test03.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova personer med Azure AD](https://aad.portal.azure.com)
