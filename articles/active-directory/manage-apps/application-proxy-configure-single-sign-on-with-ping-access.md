---
title: Rubrikbaserad autentisering med PingAccess för Azure AD Application Proxy | Microsoft-dokument
description: Publicera program med PingAccess och App Proxy för att stödja rubrikbaserad autentisering.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367979"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Rubrikbaserad autentisering för enkel inloggning med Programproxy och PingAccess

Azure Active Directory (Azure AD) Application Proxy samarbetar med PingAccess så att dina Azure AD-kunder kan komma åt fler av dina program. PingAccess utökar de [befintliga programproxyerbjudandena](application-proxy.md) så att de innehåller enkel inloggningsåtkomst till program som använder rubriker för autentisering.

## <a name="whats-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med PingAccess för Azure AD kan du ge användare åtkomst och enkel inloggning (SSO) till program som använder rubriker för autentisering. Programproxy behandlar dessa program som alla andra, med hjälp av Azure AD för att autentisera åtkomst och sedan överföra trafik via anslutningstjänsten. PingAccess sitter framför programmen och översätter åtkomsttoken från Azure AD till ett huvud. Programmet tar sedan emot autentiseringen i det format som det kan läsa.

Användarna märker inget annat när de loggar in för att använda dina företagsprogram. De kan fortfarande fungera var som helst på vilken enhet som helst. Application Proxy-anslutningsapparna dirigerar fjärrtrafik till alla appar utan hänsyn till deras autentiseringstyp, så att de fortfarande balanserar inläsningar automatiskt.

## <a name="how-do-i-get-access"></a>Hur får jag åtkomst?

Eftersom det här scenariot kommer från ett partnerskap mellan Azure Active Directory och PingAccess behöver du licenser för båda tjänsterna. Azure Active Directory Premium-prenumerationer innehåller dock en grundläggande PingAccess-licens som täcker upp till 20 program. Om du behöver publicera fler än 20 huvudbaserade program kan du köpa ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicera ditt program i Azure

Den här artikeln är för personer att publicera ett program med det här scenariot för första gången. Förutom att beskriva publiceringsstegen hjälper den dig att komma igång med både Application Proxy och PingAccess. Om du redan har konfigurerat båda tjänsterna men vill ha en uppdatering på publiceringsstegen går du till avsnittet [Lägg till ditt program i Azure AD med programproxy.](#add-your-application-to-azure-ad-with-application-proxy)

> [!NOTE]
> Eftersom det här scenariot är ett partnerskap mellan Azure AD och PingAccess finns några av instruktionerna på pingidentitetswebbplatsen.

### <a name="install-an-application-proxy-connector"></a>Installera en programproxyanslutning

Om du redan har aktiverat Application Proxy-proxy kan du hoppa över det här avsnittet och gå till [Lägg till ditt program i Azure AD med Application Proxy](#add-your-application-to-azure-ad-with-application-proxy).

Application Proxy-anslutningen är en Windows Server-tjänst som dirigerar trafiken från dina fjärranställda till dina publicerade program. Mer detaljerade installationsinstruktioner finns i [Självstudiekurs: Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör. Sidan **Azure Active Directory admin center** visas.
1. Välj **Azure Active Directory** > **Application proxy** > **download connector-tjänst**. Sidan **Hämta programproxyanslutning** visas.

   ![Hämta programproxyanslutning](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Följ installationsinstruktionerna.

Om du hämtar kopplingen ska programproxy automatiskt aktiveras för katalogen, men om inte, kan du välja **Aktivera programproxy**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Lägga till ditt program i Azure AD med programproxy

Det finns två åtgärder som du måste vidta i Azure-portalen. Först måste du publicera ditt program med Application Proxy. Sedan måste du samla in information om programmet som du kan använda under PingAccess-stegen.

#### <a name="publish-your-application"></a>Publicera programmet

Du måste först publicera din ansökan. Denna åtgärd omfattar följande:

- Lägga till ditt lokala program i Azure AD
- Tilldela en användare för att testa programmet och välja huvudbaserad SSO
- Ställa in programmets omdirigerings-URL
- Bevilja behörigheter för användare och andra program att använda ditt lokala program

Så här publicerar du ett eget lokalt program:

1. Om du inte gjorde det i det sista avsnittet loggar du in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör.
1. Välj **Företagsprogram** > **Nytt program** > Lägg till ett lokalt**program**. Sidan **Lägg till ett eget lokalt program** visas.

   ![Lägg till din egen lokala applikation](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Fyll i de obligatoriska fälten med information om ditt nya program. Använd vägledningen nedan för inställningarna.

   > [!NOTE]
   > En mer detaljerad genomgång av det här steget finns i [Lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Intern webbadress**: Normalt anger du webbadressen som tar dig till appens inloggningssida när du är i företagets nätverk. I det här fallet måste kopplingen behandla PingAccess-proxyn som programmets förstasida. Använd det `https://<host name of your PingAccess server>:<port>`här formatet: . Porten är 3000 som standard, men du kan konfigurera den i PingAccess.

      > [!WARNING]
      > För den här typen av enkel inloggning `https` måste den interna `http`webbadressen använda och inte använda .

   1. **Metod för förautentisering**: Välj **Azure Active Directory**.
   1. **Översätt URL i rubriker:** Välj **nej**.

   > [!NOTE]
   > Om detta är ditt första program använder du port 3000 för att starta och komma tillbaka för att uppdatera den här inställningen om du ändrar PingAccess-konfigurationen. För efterföljande program måste porten matcha lyssnaren som du har konfigurerat i PingAccess. Läs mer om [lyssnare i PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Välj **Lägg till**. Översiktssidan för det nya programmet visas.

Tilldela nu en användare för programtestning och välj rubrikbaserad enkel inloggning:

1. Välj **Användare och grupper** > **Lägg till användare** > **användare och grupper (\<Antal> Markerat)** i programidofältet . En lista över användare och grupper visas som du kan välja mellan.

   ![Visar listan över användare och grupper](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Välj en användare för programtestning och välj **Välj**. Kontrollera att det här testkontot har åtkomst till det lokala programmet.
1. Välj **Tilldela**.
1. Välj **Enkel inloggningsbaserad** > **rubrik**i programmets sidofält .

   > [!TIP]
   > Om det är första gången du använder rubrikbaserad enkel inloggning måste du installera PingAccess. Om du vill vara säker på att din Azure-prenumeration automatiskt associeras med pingaccess-installationen använder du länken på den här inloggningssidan för att hämta PingAccess. Du kan öppna nedladdningsplatsen nu, eller komma tillbaka till den här sidan senare.

   ![Visar rubrikbaserad inloggningsskärm och PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Välj **Spara**.

Kontrollera sedan att omdirigeringsadressen är inställd på din externa webbadress:

1. Välj Azure Active Directory**App-registreringar**i sidofältet i Azure Active Directory.In the **Azure Active Directory admin center** sidebar, select **Azure Active Directory** > App registrations . En lista över program visas.
1. Välj ditt program.
1. Välj länken **bredvid Omdirigera URI:er**och visa antalet omdirigerande URI:er som har konfigurerats för webb- och offentliga klienter. Sidan ** \<programnamn> - Autentisering** visas.
1. Kontrollera om den externa URL:en som du tidigare har tilldelat programmet finns i listan **Omdirigera URI:er.** Om den inte är det lägger du till den externa webbadressen nu med hjälp av en omdirigerings-URI-typ av **webb**och väljer **Spara**.

Konfigurera slutligen ditt lokala program så att användarna får läsåtkomst och andra program har läs-/skrivåtkomst:

1. Välj **API-behörigheter** > **Lägg till en behörighet** > microsoft**API:er** > **Microsoft Graph**i sidofältet För **appregistreringar** . Sidan **Begär API-behörighet för** Microsoft **Graph** visas, som innehåller API:erna för Windows Azure Active Directory.

   ![Visar sidan Api-behörigheter för begäran](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Välj **Delegerade behörigheter** > **Användaranvändare.Läs****User** > .
1. Välj **Programbehörigheter** > **Application** > **Application.ReadWrite.All**.
1. Välj **Lägg till behörigheter**.
1. På sidan **API-behörigheter** väljer du **Bevilja administratörsmedgivande för \<ditt katalognamn>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Samla in information för PingAccess-stegen

Du måste samla in dessa tre bitar av information (alla GUIDs) för att ställa in ditt program med PingAccess:

| Namn på Azure AD-fältet | Namn på pingaccess-fältet | Dataformat |
| --- | --- | --- |
| **Program-ID (klient)** | **Klient-ID** | GUID |
| **Katalog-ID (klient)** | **Emittenten** | GUID |
| `PingAccess key` | **Klienthemlighet** | Slumpmässig sträng |

Så här samlar du in denna information:

1. Välj Azure Active Directory**App-registreringar**i sidofältet i Azure Active Directory.In the **Azure Active Directory admin center** sidebar, select **Azure Active Directory** > App registrations . En lista över program visas.
1. Välj ditt program. Sidan **Appregistreringar** för ditt program visas.

   ![Registreringsöversikt för en ansökan](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Bredvid värdet **Program (klient)** väljer du ikonen **Kopiera till Urklipp** och kopierar och sparar det. Du anger det här värdet senare som PingAccesss klient-ID.
1. Nästa **katalog-ID-värde för katalog (klient)** väljer du även **Kopiera till Urklipp**och kopierar och sparar det. Du anger det här värdet senare som PingAccesss utfärdare.
1. Välj **Certifikat och hemligheter** > **Ny klienthemlighet**från sidofältet i **appregistreringarna** för din ansökan. Sidan **Lägg till en klienthemlighet** visas.

   ![Visar sidan Lägg till en klienthemlighet](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Skriv **Description**i `PingAccess key`Beskrivning .
1. Under **Upphör att gälla**väljer du hur du ställer in PingAccess-nyckeln: I **1 år**, I **2 år**eller **Aldrig**.
1. Välj **Lägg till**. PingAccess-nyckeln visas i tabellen över klienthemligheter, med en slumpmässig sträng som fyller i automatiskt i fältet **VÄRDE.**
1. Bredvid pingaccess-tangentens **VÄRDE-fält** väljer du ikonen **Kopiera till Urklipp** och kopierar och sparar den. Du anger det här värdet senare som PingAccesss klienthemlighet.

**Uppdatera `acceptMappedClaims` fältet:**

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) som programadministratör.
1. Välj Azure Active**Directory-appregistreringar** **Azure Active Directory** > . En lista över program visas.
1. Välj ditt program.
1. Välj **Manifest**från sidofältet på sidan **Appregistreringar** för ditt program. Manifestet JSON-koden för registreringen av din ansökan visas.
1. Sök efter `acceptMappedClaims` fältet och ändra `True`värdet till .
1. Välj **Spara**.

### <a name="use-of-optional-claims-optional"></a>Användning av valfria anspråk (valfritt)

Med valfria anspråk kan du lägga till standard-men-inte-inkluderade-som standard-anspråk som varje användare och klient har. Du kan konfigurera valfria anspråk för ditt program genom att ändra programmanifestet. Mer information finns i [artikeln Förstå Azure AD-programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Exempel på att inkludera e-postadress i access_token som PingAccess kommer att använda:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Användning av anspråksmappningsprincip (valfritt)

[Anspråksmappningsprincip (förhandsversion)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) för attribut som inte finns i AzureAD. Med anspråksmappning kan du migrera gamla on-prem-appar till molnet genom att lägga till ytterligare anpassade anspråk som backas upp av dina ADFS- eller användarobjekt

Om du vill att programmet ska använda ett anpassat anspråk och inkludera ytterligare fält måste du också [ha skapat en anpassad princip för anspråksmappning och tilldelat det till programmet](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Om du vill använda ett anpassat anspråk måste du också ha en anpassad princip definierad och tilldelad till programmet. Den här principen bör innehålla alla nödvändiga anpassade attribut.
>
> Du kan göra principdefinition och tilldelning via PowerShell eller Microsoft Graph. Om du gör dem i PowerShell kan du `New-AzureADPolicy` behöva använda det och `Add-AzureADServicePrincipalPolicy`sedan tilldela det till programmet med . Mer information finns i [Anspråksmappningsprinciptilldelning](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Exempel:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Aktivera PingAccess för att använda anpassade anspråk

Det är valfritt att aktivera PingAccess för att använda anpassade anspråk, men krävs om du förväntar dig att programmet ska använda ytterligare anspråk.

När du konfigurerar PingAccess i följande steg måste den webbsession som du ska skapa (Inställningar->Access->Web Sessions) ha **förfrågansprofilen** avmarkerad och **Uppdatera användarattribut** inställda till **Nej**

## <a name="download-pingaccess-and-configure-your-application"></a>Ladda ner PingAccess och konfigurera ditt program

Nu när du har slutfört alla installationssteg för Azure Active Directory kan du gå vidare till att konfigurera PingAccess.

De detaljerade stegen för PingAccess-delen av det här scenariot fortsätter i pingidentitetsdokumentationen. Följ instruktionerna i [Konfigurera PingAccess för Azure AD för att skydda program som publiceras med Hjälp av Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) på pingidentitetswebbplatsen.

Dessa steg hjälper dig att installera PingAccess och konfigurera ett PingAccess-konto (om du inte redan har ett). Om du sedan vill skapa en Azure AD OpenID Connect-anslutning (OIDC) konfigurerar du en tokenprovider med värdet **Katalog (klient) som** du kopierade från Azure AD-portalen. Om du vill skapa en webbsession på PingAccess använder du `PingAccess key` **program-ID:t och** värdena. Därefter kan du ställa in identitetsmappning och skapa en virtuell värd, plats och ett program.

### <a name="test-your-application"></a>Testa ditt program

När du har slutfört alla dessa steg ska programmet vara igång. Om du vill testa den öppnar du en webbläsare och navigerar till den externa URL:en som du skapade när du publicerade programmet i Azure. Logga in med testkontot som du har tilldelat programmet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PingAccess för Azure AD för att skydda program som publiceras med Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)
