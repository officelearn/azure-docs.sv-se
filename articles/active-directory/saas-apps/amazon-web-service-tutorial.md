---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Amazon Web Services (AWS) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40fd8217285643aa7d706d194d7f78ba0634dd32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Amazon Web Services (AWS)

I den här självstudien får du lära dig hur du integrerar Amazon Web Services (AWS) med Azure Active Directory (Azure AD). När du integrerar Amazon Web Services (AWS) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Amazon Web Services (AWS).
* Gör att användarna automatiskt loggas in på Amazon Web Services (AWS) med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagram över Azure AD- och AWS-relation](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Du kan konfigurera flera identifierare för flera instanser. Ett exempel:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Med dessa värden tar Azure AD **#** bort värdet för `https://signin.aws.amazon.com/saml` och skickar rätt värde som målgrupps-URL i SAML-token.

Vi rekommenderar den här metoden av följande skäl:

- Varje program ger dig ett unikt X509-certifikat. Varje instans av en AWS-appinstans kan sedan ha ett annat certifikatutgångensdatum, som kan hanteras på en enskild AWS-kontobasis. Övergripande certifikat rollover är lättare i detta fall.

- Du kan aktivera användaretablering med en AWS-app i Azure AD och sedan hämtar vår tjänst alla roller från det AWS-kontot. Du behöver inte manuellt lägga till eller uppdatera AWS-rollerna i appen.

- Du kan tilldela appens ägare individuellt för appen. Den här personen kan hantera appen direkt i Azure AD.

> [!Note]
> Kontrollera att du bara använder ett galleriprogram.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En AWS-prenumeration (Single Sign-on) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Amazon Web Services (AWS) stöder **SP- och IDP**-initierad enkel inloggning
* När du har konfigurerat Amazon Web Services (AWS) kan du framtvinga Sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbetskonto, ett skolkonto eller ett personligt Microsoft-konto.
1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
1. Välj Alla**program** **för Företagsprogram på** > översiktsmenyn i Azure Active Directory .
1. Välj **Nytt program** om du vill lägga till ett program.
1. Skriv **Amazon Web Services (AWS)** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Amazon Web Services (AWS)** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Konfigurera och testa Azure AD enkel inloggning för Amazon Web Services (AWS)

Konfigurera och testa Azure AD SSO med Amazon Web Services (AWS) med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Amazon Web Services (AWS).

Så här konfigurerar och testar du Azure AD SSO med Amazon Web Services (AWS) och slutför följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Amazon Web Services (AWS) SSO](#configure-amazon-web-services-aws-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Amazon Web Services (AWS) testanvändare](#create-amazon-web-services-aws-test-user)** - att ha en motsvarighet till B.Simon i Amazon Web Services (AWS) som är kopplad till Azure AD-representationen av användaren.
    1. **[Konfigurera rolletablering i Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan **Manage** [Azure-portalen](https://portal.azure.com/)på sidan För integrering av **Amazon Web Services (AWS)** och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att välja **Spara**.

1. När du konfigurerar mer än en instans anger du ett identifierarvärde. Från andra instansen och framåt använder **#** du följande format, inklusive ett tecken för att ange ett unikt SPN-värde.

    `https://signin.aws.amazon.com/saml#2`

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Amazon Web Services (AWS).**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
1. Välj **Användare** > **alla användare**på översiktsmenyn i Azure Active Directory .
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Amazon Web Services (AWS).

1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
1. Välj Alla**program** **för Företagsprogram på** > översiktsmenyn i Azure Active Directory .
1. Välj Amazon Web **Services (AWS) i**programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-amazon-web-services-aws-sso"></a>Konfigurera AMAson Web Services (AWS) SSO

1. I ett annat webbläsarfönster loggar du in på aws-företagets webbplats som administratör.

2. Välj **AWS Home**.

    ![Skärmbild av AWS-företagswebbplats, med AWS Home-ikonen markerad][11]

3. Välj **Identitets- och åtkomsthantering**.

    ![Skärmbild av sidan AWS-tjänster, med IAM markerat][12]

4. Välj **Identitetsleverantörer** > **Skapa provider**.

    ![Skärmbild av IAM-sidan, med identitetsleverantörer och skapa provider markerade][13]

5. Gör följande på sidan **Konfigurera provider:**

    ![Skärmbild av Konfigurera provider][14]

    a. För **providertyp**väljer du **SAML**.

    b. För **Providernamn**skriver du ett leverantörsnamn (till exempel: *WAAD*).

    c. Om du vill ladda upp den nedladdade **metadatafilen** från Azure-portalen väljer du **Välj fil**.

    d. Välj **Nästa steg**.

6. På sidan **Verifiera providerinformation** väljer du **Skapa**.

    ![Skärmbild av Verifiera leverantörsinformation, med Skapa markerad][15]

7. Välj **Roller** > **Skapa roll**.

    ![Skärmbild av sidan Roller][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Skärmbild av sidan Skapa roll][19]

    a. Under **Välj typ av betrodd entitet**väljer du **SAML 2.0-federation**.

    b. Under **Välj en SAML 2.0-provider**väljer du den **SAML-provider** som du skapade tidigare (till *exempel: WAAD*).

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Välj **Nästa: Behörigheter**.

9. Bifoga lämplig princip enligt organisationen i dialogrutan **Bifoga behörighetsprinciper.** Välj sedan **Nästa: Granska**.  

    ![Skärmbild av dialogrutan Bifoga behörighetsprincip][33]

10. Gör följande i dialogrutan **Granska:**

    ![Skärmbild av dialogrutan Granska][34]

    a. Ange ditt rollnamn i **rollnamnet.**

    b. Ange beskrivningen i **rollbeskrivningen.**

    c. Välj **Skapa roll**.

    d. Skapa så många roller som behövs och mappa dem till identitetsprovidern.

11. Använd AWS-tjänstkontoautentiseringsuppgifter för att hämta rollerna från AWS-kontot i Azure AD-användaretablering. För detta öppnar du AWS-konsolstarten.

12. Välj **Tjänster**. Under **Säkerhet, & IAM under Säkerhet, Identitetsefterlevnad**väljer du **IAM**.

    ![Skärmbild av AWS-konsolen hemma, med tjänster och IAM markerade](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Välj **Principer**i avsnittet IAM .

    ![Skärmbild av avsnittet IAM med principer markerade](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Skapa en ny princip genom att välja **Skapa princip** för att hämta rollerna från AWS-kontot i Azure AD-användaretablering.

    ![Skärmbild av skapa rollsida, med Skapa princip markerad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Skapa en egen princip för att hämta alla roller från AWS-konton.

    ![Skärmbild av sidan Skapa princip, med JSON markerat](./media/amazon-web-service-tutorial/policy1.png)

    a. Välj fliken **JSON** i **Skapa**princip.

    b. Lägg till följande JSON i principdokumentet:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Välj **Granskningsprincip** för att validera principen.

    ![Skärmbild av sidan Skapa princip](./media/amazon-web-service-tutorial/policy5.png)

16. Definiera den nya principen.

    ![Skärmbild av sidan Skapa princip, med fälten Namn och Beskrivning markerade](./media/amazon-web-service-tutorial/policy2.png)

    a. För **Namn**anger **du AzureAD_SSOUserRole_Policy**.

    b. För **Beskrivning**anger du **Den här principen gör det möjligt att hämta rollerna från AWS-konton**.

    c. Välj **Skapa princip**.

17. Skapa ett nytt användarkonto i AWS IAM-tjänsten.

    a. I AWS IAM-konsolen väljer du **Användare**.

    ![Skärmbild av AWS IAM-konsolen, med användare markerade](./media/amazon-web-service-tutorial/policy3.png)

    b. Om du vill skapa en ny användare väljer du **Lägg till användare**.

    ![Skärmbild av knappen Lägg till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I avsnittet **Lägg till användare:**

    ![Skärmbild av Lägg till användarsida, med användarnamn och åtkomsttyp markerad](./media/amazon-web-service-tutorial/adduser1.png)

    * Ange användarnamnet som **AzureADRoleManager**.

    * För åtkomsttypen väljer du **Programmatisk åtkomst**. På så sätt kan användaren anropa API:erna och hämta rollerna från AWS-kontot.

    * Välj **Nästa behörighet .**

18. Skapa en ny princip för den här användaren.

    ![Skärmbild av Lägg till användare](./media/amazon-web-service-tutorial/adduser2.png)

    a. Välj **Bifoga befintliga principer direkt**.

    b. Sök efter den nyligen skapade principen i filteravsnittet **AzureAD_SSOUserRole_Policy**.

    c. Markera principen och välj sedan **Nästa: Granska**.

19. Granska principen till den bifogade användaren.

    ![Skärmbild av Lägg till användarsida med Skapa användare markerad](./media/amazon-web-service-tutorial/adduser3.png)

    a. Granska användarnamnet, åtkomsttypen och den princip som är mappad till användaren.

    b. Välj **Skapa användare**.

20. Hämta en användares användaruppgifter.

    ![Skärmbild av Lägg till användare](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopiera användarens **åtkomstnyckel-ID** och **hemliga åtkomstnyckel**.

    b. Ange dessa autentiseringsuppgifter i avsnittet Etablera azure AD-användare för att hämta rollerna från AWS-konsolen.

    c. Välj **Stäng**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Konfigurera rolletablering i Amazon Web Services (AWS)

1. I Azure AD-hanteringsportalen går du till **Etablering**i AWS-appen .

    ![Skärmbild av AWS-appen, med etablering markerad](./media/amazon-web-service-tutorial/provisioning.png)

2. Ange åtkomstnyckeln och hemligheten i fälten **clientsecret** respektive **Secret Token.**

    ![Skärmbild av dialogrutan Administratörsautentiseringsuppgifter](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Ange AWS-användaråtkomstnyckeln i fältet **clientsecret**.

    b. Ange AWS-användarhemligheten i fältet **Hemlig token**.

    c. Välj **Testanslutning**.

    d. Spara inställningen genom att välja **Spara**.

3. Välj **På**för **etableringsstatus**i avsnittet Inställningar i avsnittet **Inställningar.** Välj sedan **Spara**.

    ![Skärmbild av avsnittet Inställningar med På markerad](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Etableringstjänsten importerar roller endast från AWS till Azure AD. Tjänsten etablerar inte användare och grupper från Azure AD till AWS.

> [!NOTE]
> När du har sparat etableringsautentiseringsuppgifterna måste du vänta tills den första synkroniseringscykeln körs. Synkronisering tar vanligtvis cirka 40 minuter att slutföra. Du kan se statusen längst ned på **etableringssidan** under **Aktuell status**.

### <a name="create-amazon-web-services-aws-test-user"></a>Skapa Amazon Web Services (AWS)-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter B.Simon i Amazon Web Services (AWS). För Amazon Web Services (AWS) krävs det inte att en användare skapas i deras system för enkel inloggning, så du behöver inte göra någonting här.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Amazon Web Services (AWS)-panelen i åtkomstpanelen bör du automatiskt loggas in på Amazon Web Services (AWS) som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Kända problem

 * I avsnittet **Etablering** visas underavsnittet **Mappningar** i avsnittet "Inläsning..." meddelandet och visar aldrig attributmappningarna. Det enda etableringsarbetsflödet som stöds idag är importen av roller från AWS till Azure AD för val under en användar- eller grupptilldelning. Attributmappningarna för detta är förutbestämda och kan inte konfigureras.

 * Avsnittet **Etablering** stöder endast inmatning av en uppsättning autentiseringsuppgifter för en AWS-klientorganisation i taget. Alla importerade roller skrivs till egenskapen `appRoles` för Azure [ `servicePrincipal` AD-objektet](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) för AWS-klienten.

   Flera AWS-klienter (representeras av) `servicePrincipals`kan läggas till i Azure AD från galleriet för etablering. Det finns ett känt problem, men med att inte kunna automatiskt skriva alla `servicePrincipals` importerade roller från flera `servicePrincipal` AWS som används för etablering till den enda som används för SSO.

   Som en lösning kan du använda [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) `appRoles` för att extrahera `servicePrincipal` alla importerade till varje AWS där etablering är konfigurerad. Du kan sedan lägga till dessa `servicePrincipal` rollsträngar i AWS där SSO är konfigurerat.

* Roller måste uppfylla följande krav för att kunna importeras från AWS till Azure AD:

  * Roller måste ha exakt en saml-leverantör definierad i AWS

  * Den sammanlagda längden på rollen ARN och saml-leverantören ARN för en roll som importeras måste vara 119 tecken eller mindre

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Amazon Web Services (AWS) med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Amazon Web Services (AWS) med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-aws)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
