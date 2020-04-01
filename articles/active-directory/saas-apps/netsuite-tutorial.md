---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med NetSuite | Microsoft-dokument'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a920e58f1ffd4c3e3e9769bf6346100a8677b90
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760068"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Självstudiekurs: Integrera Azure AD enkel inloggning (SSO) med NetSuite

I den här självstudien får du lära dig hur du integrerar NetSuite med Azure Active Directory (Azure AD). När du integrerar NetSuite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till NetSuite.
* Gör att användarna automatiskt loggas in på NetSuite med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En NetSuite-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. 

NetSuite stöder:

* IDP-initierad SSO.
* JIT (just-in-time) användaretablering.
* [Automatisk etablering av användare](NetSuite-provisioning-tutorial.md).
* När du har konfigurerat NetSuite kan du framtvinga sessionskontroller, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Eftersom identifieraren för det här programmet är ett fast strängvärde kan endast en instans konfigureras i en klient.

## <a name="add-netsuite-from-the-gallery"></a>Lägg till NetSuite från galleriet

Om du vill konfigurera integreringen av NetSuite i Azure AD lägger du till NetSuite från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i den vänstra rutan.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv NetSuite i sökrutan i avsnittet **Lägg till från galleriet.** **NetSuite**
1. Välj **NetSuite**i resultatfönstret och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Konfigurera och testa en azure AD-inloggning för NetSuite

Konfigurera och testa Azure AD SSO med NetSuite med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i NetSuite.

Om du vill konfigurera och testa Azure AD SSO med NetSuite slutför du följande byggblock:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    * [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD enkel inloggning med användaren B.Simon.  
    * [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att användaren B.Simon ska kunna använda azure AD-enkel inloggning.
1. [Konfigurera NetSuite SSO](#configure-netsuite-sso) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * [Skapa NetSuite-testanvändaren](#create-the-netsuite-test-user) för att ha en motsvarighet till användaren B.Simon i NetSuite som är länkad till Azure AD-representationen för användaren.
1. [Testa SSO](#test-sso) för att kontrollera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Så här aktiverar du Azure AD SSO i Azure-portalen:

1. Leta efter avsnittet Hantera på sidan **Hantera** på sidan **NetSuite-program** i [Azure-portalen](https://portal.azure.com/)och välj sedan **Enkel inloggning**.
1. I fönstret **Välj en metod för enkel inloggning** väljer du **SAML**.
1. I fönstret Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** ("penna") **bredvid Grundläggande SAML-konfiguration**. **Set up Single Sign-On with SAML**

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Skriv en URL i något av följande format i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration:**

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    * Du kommer ** < `Account ID` ** att få värdet i Netsuite konfiguration avsnitt som förklaras senare i handledningen på steg 8 under Netsuite Configuration. Du hittar den exakta domänen (till exempel system.na0.netsuite.com i det här fallet).

        ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Värdena i föregående webbadresser är inte verkliga. Uppdatera dem med den faktiska svars-URL:en. Kontakta [NetSuite Client-supportteamet](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)för att få värdet. Du kan också referera till de format som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. NetSuite-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig NetSuite-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut |
    | ---------------| --------------- |
    | konto  | `account id` |

    > [!NOTE]
    > Värdet för kontoattributet är inte verkligt. Du kommer att uppdatera det här värdet, som förklaras senare i den här självstudien.

1. På sidan Konfigurera enkel inloggning med SAML i avsnittet SAML-signeringscertifikat hittar du XML för federationsmetadata och väljer Hämta för att hämta certifikatet och spara det på datorn.

    ![Länken Hämta certifikat](common/metadataxml.png)

1. I avsnittet **Konfigurera NetSuite** kopierar du rätt WEBBADRESS eller WEBBADRESSER, beroende på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen som heter B.Simon.

1. I den vänstra rutan i Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

1. Välj **Ny användare** högst upp på skärmen.

1. Gör så här i fönstret **Användaregenskaper:**

   a. Ange **B.Simon**i rutan **Namn** .  
   b. I rutan **Användarnamn** anger username@companydomain.extension du (till B.Simon@contoso.comexempel ).  
   c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.  
   d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du använda användare B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till NetSuite.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **NetSuite**i programlistan .
1. Leta efter avsnittet **Hantera** i översiktsfönstret och välj sedan länken **Användare och grupper.**

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan Användare och **grupper**i fönstret Lägg **till tilldelning** .

    ![Knappen "Lägg till användare"](common/add-assign-user.png)

1. Välj **B.Simon**i listrutan **Användare** **och grupper** och välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet gör du följande:

   a. Välj lämplig roll för användaren i listrutan Välj roll i fönstret **Välj roll.**  
   b. Längst ned på skärmen väljer du knappen **Välj.**
1. Markera knappen **Tilldela** i fönstret **Lägg till tilldelning.**

## <a name="configure-netsuite-sso"></a>Konfigurera NetSuite SSO

1. Öppna en ny flik i webbläsaren och logga in på din NetSuite-företagswebbplats som administratör.

2. I det övre navigeringsfältet väljer du **Installationsprogrammet**och väljer sedan > **Företagsaktiveringsfunktioner**. **Company**

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Välj **SuiteCloud**i verktygsfältet i mitten av sidan .

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Markera kryssrutan **SAML Enkel inloggning** under **Hantera autentisering**för att aktivera alternativet SAML enkel inloggning i NetSuite.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Välj **Installation**i det övre navigeringsfältet .

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

6. Välj **Integrering**i listan **Inställningsuppgifter** .

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-integration.png)

7. Under **Hantera autentisering**väljer du **SAML Enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml.png)

8. Gör följande under NetSuite-konfiguration i **fönstret SAML-installation** under **NetSuite-konfiguration:**

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Markera kryssrutan **Primär autentiseringsmetod.**

    b. Under **SAMLV2 Identity Provider Metadata**väljer du Ladda upp **IDP-metadatafil**och väljer sedan **Bläddra** för att ladda upp metadatafilen som du hämtade från Azure-portalen.

    c. Välj **Skicka**.

9. I det övre navigeringsfältet NetSuite väljer du **Installationsprogrammet**och väljer sedan > **Företagsinformation**. **Company**

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-account-id.png)

    b. Kopiera värdet **för konto-ID** i den högra kolumnen i fönstret **Företagsinformation.**

    c. Klistra in **konto-ID:t** som du kopierade från NetSuite-kontot i rutan **Attributvärde** i Azure AD.

10. Innan användare kan utföra enkel inloggning till NetSuite så måste de först tilldelas lämpliga behörigheter i NetSuite. Så här tilldelar du dessa behörigheter:

    a. Välj **Installation**i det övre navigeringsfältet .

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    b. Välj **Användare/roller**i den vänstra rutan och välj sedan **Hantera roller**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Välj **Ny roll**.

    d. Ange ett **namn** för den nya rollen.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-new-role.png)

    e. Välj **Spara**.

    f. Välj Behörigheter i det övre **navigeringsfältet**. Välj sedan **Installation .**

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-sso.png)

    g. Välj **SAML Enkel inloggning**och välj sedan Lägg **till**.

    h. Välj **Spara**.

    i. I det övre navigeringsfältet väljer du **Installationsprogrammet**och väljer sedan **Installationshanteraren**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    j. Välj **Användare/roller**i den vänstra rutan och välj sedan **Hantera användare**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Välj en testanvändare, välj **Redigera**och välj sedan fliken **Access.**

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-edit-user.png)

    l. I fönstret **Roller** tilldelar du lämplig roll som du har skapat.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-add-role.png)

    m. Välj **Spara**.

### <a name="create-the-netsuite-test-user"></a>Skapa NetSuite-testanvändaren

I det här avsnittet skapas en användare som heter B.Simon i NetSuite. NetSuite stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i NetSuite så skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer NetSuite-panelen på åtkomstpanelen ska du automatiskt loggas in på den NetSuite som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Prova NetSuite med Azure AD](https://aad.portal.azure.com/)
- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du NetSuite med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)