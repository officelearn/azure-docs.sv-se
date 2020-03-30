---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med ServiceNow | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/25/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe6c857e5b0c2f48f27c167c177dbf1f4651986
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384117"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med ServiceNow

I den här självstudien får du lära dig hur du integrerar ServiceNow med Azure Active Directory (Azure AD). När du integrerar ServiceNow med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ServiceNow.
* Aktivera dina användare så att de automatiskt loggas in på ServiceNow med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En ServiceNow-prenumeration (Single Sign-on) aktiverad prenumeration.
* För ServiceNow, en instans eller innehavare av ServiceNow, Calgary version eller senare.
* För ServiceNow Express, en instans av ServiceNow Express, Helsingfors version eller senare.
* ServiceNow-klientorganisationen måste ha [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (Plugin-program för enkel inloggning med flera providrar) aktiverat. Du kan göra detta genom [att skicka en servicebegäran](https://hi.service-now.com).
* Om du vill ha automatisk konfiguration aktiverar du plugin-programmet för flera providrar för ServiceNow.
* Om du vill installera Programmet ServiceNow Classic (Mobil) går du till rätt butik och söker efter Programmet ServiceNow Classic. Ladda sedan ner den.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. 

* ServiceNow stöder **SP** initierade SSO.

* ServiceNow stöder [automatisk användaretablering](servicenow-provisioning-tutorial.md).

* När du har konfigurerat ServiceNow kan du framtvinga sessionskontroller som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Du kan konfigurera ServiceNow Classic (Mobile) programmet med Azure AD för att aktivera SSO. Den stöder både Android och iOS-användare. I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

## <a name="add-servicenow-from-the-gallery"></a>Lägg till ServiceNow från galleriet

För att konfigurera integreringen av ServiceNow till Azure AD behöver du lägga till ServiceNow från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller med hjälp av ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i den vänstra rutan.
1. Gå till **Enterprise Applications**och välj **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. I avsnittet **Lägg till från galleriet** anger du **ServiceNow** i sökrutan.
1. Välj **ServiceNow** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera och testa azure AD enkel inloggning för ServiceNow

Konfigurera och testa Azure AD SSO med ServiceNow med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i ServiceNow.

Så här konfigurerar och testar du Azure AD SSO med ServiceNow:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD enkel inloggning med B.Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B.Simon att använda Azure AD enkel inloggning.
    1. [Konfigurera Azure AD SSO för ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) så att användarna kan använda den här funktionen.
2. [Konfigurera ServiceNow](#configure-servicenow) för att konfigurera SSO-inställningarna på programsidan.
    1. [Skapa en ServiceNow-testanvändare](#create-servicenow-test-user) för att ha en motsvarighet till B.Simon i ServiceNow, länkad till Azure AD-representationen för användaren.
    1. [Konfigurera ServiceNow Express SSO](#configure-servicenow-express-sso) för att konfigurera de enskilda inloggningsinställningarna på programsidan.  
3. [Testa SSO](#test-sso) för att kontrollera om konfigurationen fungerar.
4. [Testa SSO för ServiceNow Classic (Mobil) för](#test-sso-for-servicenow-classic-mobile) att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan ServiceNow-programintegrering på **ServiceNow** **Azure-portalen.** [Azure portal](https://portal.azure.com/) Välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. Gör följande i avsnittet **Grundläggande SAML-konfiguration:**

    a. I **Sign on URL**anger du en URL som använder följande mönster:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. I **Identifierare (Entitets-ID)** anger du en URL som använder följande mönster:`https://<instance-name>.service-now.com`

    c. För **Svars-URL**anger du en av följande WEBBADRESSER:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren, vilket förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Sök upp **certifikat (Base64)** i avsnittet **SAML Signering Certificate** på sidan Konfigurera enkel inloggning med **SAML.** 

   ![Skärmbild av avsnittet SAML-signeringscertifikat, med Hämta markerat](common/certificatebase64.png)

   a. Välj kopieringsknappen om du vill kopiera **Url till metadata för App Federation**och klistra in den i Anteckningar. Den här webbadressen kommer att användas senare i självstudien.

    b. Välj **Hämta** om du vill hämta **Certifikat(Base64)** och spara sedan certifikatfilen på datorn.

1. I avsnittet **Konfigurera ServiceNow** kopierar du lämpliga webbadresser baserat på dina behov.

   ![Skärmbild av avsnittet Konfigurera ServiceNow, med webbadresser markerade](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare, kallad B.Simon, i Azure-portalen.

1. Välj Azure Active Directory**Users** > **All-användare**i den vänstra rutan i **Azure-portalen** > .
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. För **Namn** `B.Simon`anger du .  
   1. För **Användarnamn**anger username@companydomain.extensiondu . Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösenord**och skriv sedan ned värdet som visas i rutan **Lösenord.**
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ServiceNow.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.
1. I programlistan väljer du **ServiceNow**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Skärmbild av avsnittet Hantera, med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Skärmbild av användare och grupper, med Lägg till användare markerad](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i användarlistan och väljer sedan **Välj**.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Välj sedan **Välj**.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurera Azure AD SSO för ServiceNow Express

1. I [Azure-portalen](https://portal.azure.com/)väljer du enkel inloggning på sidan **ServiceNow-programintegration** . **single sign-on**

    ![Skärmbild av sidan för integrering av TjänstenNow-program, med Enkel inloggning markerad](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Skärmbild av Välj en enda inloggningsmetod, med SAML markerat](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för att öppna dialogrutan Grundläggande **SAML-konfiguration.**

    ![Skärmbild av Konfigurera enkel inloggning med SAML-sida, med pennikonen markerad](common/edit-urls.png)

4. Gör följande i avsnittet **Grundläggande SAML-konfiguration:**

    a. För **Inloggnings-URL**anger du en URL som använder följande mönster:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. För **identifierare (entitets-ID)** anger du en URL som använder följande mönster:`https://<instance-name>.service-now.com`

    c. För **Svars-URL**anger du en av följande WEBBADRESSER:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren, vilket förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan Konfigurera enkel inloggning med SAML väljer du **Hämta** i avsnittet **SAML-signeringscertifikat** för att hämta **certifikatet (Base64)** från de angivna alternativen enligt dina krav. **Set up single sign-on with SAML** Spara den på datorn.

    ![Skärmbild av avsnittet SAML-signeringscertifikat med hämtning markerad](common/certificatebase64.png)

6. Du kan låta Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering. Om du vill aktivera den här tjänsten går du till avsnittet **Konfigurera ServiceNow** och väljer **Visa steg-för-steg-instruktioner** för att öppna fönstret **Konfigurera inloggning.**

    ![Skärmbild av avsnittet Konfigurera ServiceNow, med visa steg-för-steg-instruktioner markerade](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. I formuläret **Konfigurera inloggning** anger du ditt ServiceNow-förekomstnamn, administratörsanvändarnamn och administratörslösenord. Välj **Konfigurera nu**. Administratörsanvändarnamnet måste ha **den security_admin** rollen tilldelad i ServiceNow för att detta ska fungera. Om du vill konfigurera ServiceNow manuellt så att den använder Azure AD som SAML-identitetsprovider väljer du **Konfigurera en enda inloggning manuellt**. Kopiera **url:en för utloggning, Azure AD-identifierare och inloggnings-URL:en** från avsnittet Snabbreferens.

    ![Skärmbild av konfigurera inloggningsformulär, med Konfigurera nu markerat](./media/servicenow-tutorial/configure.png "Konfigurera appens URL")

## <a name="configure-servicenow"></a>Konfigurera ServiceNow

1. Logga in på ditt ServiceNow-program som administratör.

1. Aktivera **plugin-programmet Integration – flera provider-installationsprogrammet** för installationsprogrammet genom att följa dessa steg:

    a. Sök efter avsnittet **Systemdefinition** i sökrutan i den vänstra rutan och välj sedan **Plugins**.

    ![Skärmbild av avsnittet Systemdefinition, med systemdefinition och insticksprogram markerade](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktivera plugin")

    b. Sök efter **integration - En enda signeringsinstallationsprogram för flera provider .**

     ![Skärmbild av sidan Systeminstickning, med integration – installationsprogrammet för flera leverantörer med enkel inloggning markerad](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktivera plugin")

    c. Välj plugin-programmet. Högerklicka och välj **Aktivera/uppgradera**.

     ![Skärmbild av snabbmenyn för plugin-programmet med Aktivera/uppgradera markerad](./media/servicenow-tutorial/tutorial_activate.png "Aktivera plugin")

    d. Välj **Aktivera**.

     ![Skärmbild av dialogrutan Aktivera plugin, med Aktivera markerad](./media/servicenow-tutorial/tutorial_activate1.png "Aktivera plugin")

1. Sök efter **SSO-avsnittet med flera provider** i sökfältet i den vänstra rutan och välj sedan **Egenskaper**.

    ![Skärmbild av SSO-sektionen med flera leverantörer med SSO och egenskaper för flera leverantörer markerade](./media/servicenow-tutorial/tutorial_servicenow_06.png "Konfigurera appens URL")

1. Gör följande i dialogrutan **SSO-egenskaper för flera provider:**

    ![Skärmbild av dialogrutan SSO-egenskaper för flera providerer](./media/servicenow-tutorial/ic7694981.png "Konfigurera appens URL")

    * För **Aktivera SSO för flera providerer**väljer du **Ja**.
  
    * För **Aktivera automatisk import av användare från alla identitetsleverantörer till användartabellen**väljer du **Ja**.

    * För **Aktivera felsökningsloggning för SSO-integrering med flera providerer**väljer du **Ja**.

    * För **fältet i användartabellen som...** anger du **e-post**.
  
    * Välj **Spara**.

1. Du kan konfigurera ServiceNow automatiskt eller manuellt. Så här konfigurerar du ServiceNow automatiskt:

    1. Gå tillbaka till den enda inloggningssidan för **ServiceNow** i Azure-portalen.

    1. Konfigurera tjänsten med ett klick tillhandahålls för ServiceNow. Om du vill aktivera den här tjänsten går du till avsnittet **ServiceNow-konfiguration** och väljer **Konfigurera ServiceNow** för att öppna fönstret **Konfigurera inloggning.**

        ![Skärmbild av Konfigurera ServiceNow, med visa steg-för-steg-instruktioner markerade](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. I formuläret **Konfigurera inloggning** anger du ditt ServiceNow-förekomstnamn, administratörsanvändarnamn och administratörslösenord. Välj **Konfigurera nu**. Administratörsanvändarnamnet måste ha **den security_admin** rollen tilldelad i ServiceNow för att detta ska fungera. Om du vill konfigurera ServiceNow manuellt så att den använder Azure AD som SAML-identitetsprovider väljer du **Konfigurera en enda inloggning manuellt**. Kopiera **signerings-URL:en, SAML-enhets-ID och SAML-url för enkel inloggning från** avsnittet Snabbreferens.

        ![Skärmbild av konfigurera inloggningsformulär, med Konfigurera nu markerat](./media/servicenow-tutorial/configure.png "Konfigurera appens URL")

    1. Logga in på ditt ServiceNow-program som administratör.

       * I den automatiska konfigurationen konfigureras alla nödvändiga inställningar på **ServiceNow-sidan,** men **X.509-certifikatet** är inte aktiverat som standard. Du måste mappa den manuellt till din identitetsleverantör i ServiceNow. Följ de här stegen:

         1. Sök efter **SSO-avsnittet med flera provider** i sökrutan i den vänstra rutan och välj **Identitetsleverantörer**.

            ![Skärmbild av SSO-avsnitt med flera leverantörer, med identitetsleverantörer markerade](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

         1. Välj den automatiskt genererade identitetsprovidern.

            ![Skärmbild av identitetsleverantörer, med automatiskt genererad identitetsprovider markerad](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurera enkel inloggning")

         1.  I avsnittet **Identity Provider** (Identitetsprovider) utför du följande steg:

             ![Skärmbild av avsnittet Identitetsprovider](./media/servicenow-tutorial/automatic_config.png "Konfigurera enkel inloggning")

               * För **Namn**anger du ett namn för konfigurationen (till exempel **Microsoft Azure Federerad enkel inloggning).**

               * Ta bort den ifyllda **identitetsproviderns SingleLogoutRequest-värde** från textrutan.

               * Kopiera **värdet för ServiceNow-startsidan** och klistra in det i **inloggnings-URL** i avsnittet **ServiceNow Basic SAML-konfiguration** i Azure-portalen.

                  > [!NOTE]
                  > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

              * Kopiera värdet **entity ID/Issuer** och klistra in det i **Identifierare** i avsnittet **ServiceNow Basic SAML-konfiguration** i Azure-portalen.

              * Bekräfta att **NameID-principen** är satt till `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` värde. 

         1. Bläddra ned till **x.509-certifikatavsnittet** och välj **Redigera**.

             ![Skärmbild av x.509-certifikatavsnittet med Redigera markerad](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurera enkel inloggning")

         1. Markera certifikatet och välj högerpilsikonen för att lägga till certifikatet

            ![Skärmbild av Samling, med certifikat- och högerpilsikonen markerad](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurera enkel inloggning")

          1. Välj **Spara**.

          1. I det övre högra hörnet på sidan väljer du **Testa anslutning**.

             ![Skärmbild av sidan, med Testanslutning markerat](./media/servicenow-tutorial/tutorial_activate2.png "Aktivera plugin")

             > [!NOTE]
             > Om testanslutningen misslyckas och du inte kan aktivera den här anslutningen erbjuder ServiceNow åsidosättningsväxeln. Du måste gå in **Sys_properties. LISTA** i **söknavigeringen** och den öppnas den nya sidan systemegenskaper. Här måste du skapa en ny egenskap med namnet som **glide.authenticate.multisso.test.connection.mandatory** med **datatyp** som **Sant/Falskt** och sedan ange **värdet** som **Falskt**.

             > ![Skärmbild av sidan Testresultat](./media/servicenow-tutorial/testconnection-fail.png "Konfigurera enkel inloggning")
        
          1. Ange dem när du tillfrågas om dina autentiseringsuppgifter. Följande sida visas. Felet **SSO-utloggningstestresultat** förväntas. Ignorera felet och välj **Aktivera**.

             ![Skärmbild av sidan Testresultat](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")
  
1. Så här konfigurerar du **ServiceNow** manuellt:

    1. Logga in på ditt ServiceNow-program som administratör.

    1. Välj **Identitetsleverantörer i**den vänstra rutan .

        ![Skärmbild av SSO för flera leverantörer, med identitetsleverantörer markerade](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    1. Välj **Ny**i dialogrutan **Identitetsleverantörer** .

        ![Skärmbild av dialogrutan Identitetsleverantörer med Ny markerad](./media/servicenow-tutorial/ic7694977.png "Konfigurera enkel inloggning")

    1. Välj **SAML**i dialogrutan **Identitetsleverantörer** .

        ![Skärmbild av dialogrutan Identitetsleverantörer, med SAML markerat](./media/servicenow-tutorial/ic7694978.png "Konfigurera enkel inloggning")

    1. I **Importera identitetsprovidermetadata**utför du följande steg:

        ![Skärmbild av metadata för import av identitetsprovider, med URL och Importera markerat](./media/servicenow-tutorial/idp.png "Konfigurera enkel inloggning")

        1. Ange **url:en för appfederationsmetadata** som du har kopierat från Azure-portalen.

        1. Välj **Importera**.

    1. Den läser IdP metadata URL, och fyller alla fält information.

        ![Skärmbild av identitetsprovider](./media/servicenow-tutorial/ic7694982.png "Konfigurera enkel inloggning")

        * För **Namn**anger du ett namn för konfigurationen (till exempel **Microsoft Azure Federerad enkel inloggning).**

        * Ta bort den ifyllda **identitetsproviderns SingleLogoutRequest-värde** från textrutan.

        * Kopiera **värdet för ServiceNows startsida.** Klistra in den i **inloggnings-URL** i avsnittet **ServiceNow Basic SAML-konfiguration** i Azure-portalen.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera värdet **för entitets-ID/Utfärdare.** Klistra in den i **identifieraren** i avsnittet **Basic SAML-konfiguration** för ServiceNow Basic i Azure-portalen.

        * Bekräfta att **NameID-principen** är satt till `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` värde.

        * Välj **Avancerat**. Ange **e-post** **i användarfält**.

            > [!NOTE]
            > Du kan konfigurera Azure AD för att avge antingen Azure AD-användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token. Gör detta genom att gå till **avsnittet ServiceNow-attribut** > **Attributes** > **Enkel inloggning i Azure-portalen** och mappa önskat fält till **namnidentifierarattributet.** Värdet som lagras för det valda attributet i Azure AD (till exempel användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name).

        * Välj **Testa anslutning** längst upp till höger på sidan.

          > [!NOTE]
          > Om testanslutningen misslyckas och du inte kan aktivera den här anslutningen erbjuder ServiceNow åsidosättningsväxeln. Du måste gå in **Sys_properties. LISTA** i **söknavigeringen** och den öppnas den nya sidan systemegenskaper. Här måste du skapa en ny egenskap med namnet som **glide.authenticate.multisso.test.connection.mandatory** med **datatyp** som **Sant/Falskt** och sedan ange **värdet** som **Falskt**.

          > ![Skärmbild av sidan Testresultat](./media/servicenow-tutorial/testconnection-fail.png "Konfigurera enkel inloggning")

        * Ange dem när du tillfrågas om dina autentiseringsuppgifter. Följande sida visas. Felet **SSO-utloggningstestresultat** förväntas. Ignorera felet och välj **Aktivera**.

          ![Skärmbild av sidan Testresultat](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")

### <a name="create-servicenow-test-user"></a>Skapa ServiceNow-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i ServiceNow. ServiceNow stöder automatisk användaretablering, vilket är aktiverat som standard.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [ServiceNow-klientsupportteamet](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Konfigurera ServiceNow Express SSO

1. Logga in på ditt ServiceNow Express-program som administratör.

2. Välj Enkel inloggning **i**den vänstra rutan .

    ![Skärmbild av ServiceNow Express-programmet, med enkel inloggning markerad](./media/servicenow-tutorial/ic7694980ex.png "Konfigurera appens URL")

3. I dialogrutan **Enkel inloggning** väljer du konfigurationsikonen längst upp till höger och anger följande egenskaper:

    ![Skärmbild av dialogrutan Enkel inloggning](./media/servicenow-tutorial/ic7694981ex.png "Konfigurera appens URL")

    a. Växla **Enable multiple provider SSO** (Aktivera enkel inloggning med flera providrar) till höger.

    b. Växla **Enable debug logging for the multiple provider SSO integration** (Aktivera felsökningsloggning för integreringen av enkel inloggning med flera providrar) till höger.

    c. I **fältet i användartabellen som...** anger **du user_name**.

4. Välj Lägg till nytt certifikat **i**dialogrutan Enkel **inloggning** .

    ![Skärmbild av dialogrutan Enkel inloggning, med Lägg till nytt certifikat markerat](./media/servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")

5. Gör så här i dialogrutan **X.509-certifikat:**

    ![Skärmbild av dialogrutan X.509-certifikat](./media/servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. För **Namn**anger du ett namn för konfigurationen (till exempel **TestSAML2.0**).

    b. Välj **Active** (Aktiv).

    c. För **Format**väljer du **PEM**.

    d. För **Typ**väljer du **Förtroende store-certifikat**.

    e. Öppna ditt Base64-kodade certifikat som hämtats från Azure-portalen i Anteckningar. Kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **PEM-certifikat.**

    f. Välj **uppdatering**

6. Välj **Lägg till ny IdP i**dialogrutan Enkel **inloggning** .

    ![Skärmbild av dialogrutan Enkel inloggning, med Lägg till ny IdP markerad](./media/servicenow-tutorial/ic7694976ex.png "Konfigurera enkel inloggning")

7. Gör följande under **Konfigurera identitetsprovider under Konfigurera identitetsprovider**i dialogrutan **Lägg till ny identitetsprovider:**

    ![Skärmbild av dialogrutan Lägg till ny identitetsprovider](./media/servicenow-tutorial/ic7694982ex.png "Konfigurera enkel inloggning")

    a. För **Namn**anger du ett namn för konfigurationen (till **exempel: SAML 2.0**).

    b. För **URL för identitetsprovider**klistrar du in värdet för identitetsprovider-ID som du kopierade från Azure-portalen.

    c. För **Identitetsproviderns AuthnRequest**klistrar du in värdet för URL:en för autentiseringsbegäran som du kopierade från Azure-portalen.

    d. För **Identity Provider's SingleLogoutRequest**klistrar du in värdet för den utloggningsadress som du kopierade från Azure-portalen.

    e. För **identitetsprovidercertifikat**väljer du det certifikat som du skapade i föregående steg.

8. Välj **Avancerade inställningar**. Under **Ytterligare identitetsprovideregenskaper**utför du följande steg:

    ![Skärmbild av dialogrutan Lägg till ny identitetsprovider, med avancerade inställningar markerade](./media/servicenow-tutorial/ic7694983ex.png "Konfigurera enkel inloggning")

    a. För **protokollbindning för IDP:s SingleLogoutRequest**anger du **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. För **NameID-princip**anger du **urn:oasis:names:tc:SAML:1.1:nameid-format:ospecificerad**.

    c. För **AuthnContextClassRef-metod**anger du `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. För **Skapa en AuthnContextClass**växlar du den till av (omarkerad).

9. Under **Additional Service Provider Properties** (Ytterligare egenskaper för tjänstleverantör) utför du följande steg:

    ![Skärmbild av dialogrutan Lägg till ny identitetsprovider, med olika egenskaper markerade](./media/servicenow-tutorial/ic7694984ex.png "Konfigurera enkel inloggning")

    a. För **ServiceNows startsida**anger du webbadressen till din ServiceNow-instanss startsida.

    > [!NOTE]
    > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

    b. För **entitets-ID/Utfärdare**anger du url:en till din ServiceNow-klient.

    c. För **Audience URI**anger du url:en till din ServiceNow-klientorganisation.

    d. För **Klocksnedställning**anger du **60**.

    e. För **Användarfält**anger du **e-post**.

    > [!NOTE]
    > Du kan konfigurera Azure AD för att avge antingen Azure AD-användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token. Gör detta genom att gå till **avsnittet ServiceNow-attribut** > **Attributes** > **Enkel inloggning i Azure-portalen** och mappa önskat fält till **namnidentifierarattributet.** Värdet som lagras för det valda attributet i Azure AD (till exempel användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name).

    f. Välj **Spara**.

## <a name="test-sso"></a>Testa SSO

När du väljer panelen ServiceNow på åtkomstpanelen ska du automatiskt loggas in på den ServiceNow som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testa SSO för ServiceNow Classic (mobil)

1. Öppna ditt **ServiceNow Classic-program (Mobile)** och utför följande steg:

    a. Välj plustecknet i det nedre högra hörnet.

    ![Skärmbild av ServiceNow Classic-programmet, med plustecknet markerat](./media/servicenow-tutorial/test03.png)

    b. Ange ditt ServiceNow-förekomstnamn och välj **Fortsätt**.

    ![Skärmbild av sidan Lägg till instans, med Fortsätt markerat](./media/servicenow-tutorial/test04.png)

    c. Gör **Log in** följande på inloggningssidan:

    ![Skärmbild av Inloggningssida, med Använd extern inloggning markerad](./media/servicenow-tutorial/test01.png)

    *  Ange **användarnamn** B.simon@contoso.com, till exempel .

    *  Välj **ANVÄND EXTERN INLOGGNING**. Du omdirigeras till Azure AD-sidan för inloggning.

    *  Ange autentiseringsuppgifter. Om det finns någon autentisering från tredje part, eller någon annan säkerhetsfunktion aktiverad, måste användaren svara därefter. Programmets **startsida** visas.

        ![Skärmbild av programmets startsida](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](servicenow-provisioning-tutorial.md)

- [Prova ServiceNow med Azure AD](https://aad.portal.azure.com)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [Så här skyddar du ServiceNow med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)