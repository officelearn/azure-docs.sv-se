---
title: Microsoft Graph-API för Azure Active Directory Identity Protection
description: Lär dig hur du frågar Microsoft Graph risk identifieringar och tillhör ande information från Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5367e5027bfae2fa3ed7e87a779e50e4048ba608
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861739"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Kom igång med Azure Active Directory Identity Protection och Microsoft Graph

Microsoft Graph är Microsoft Unified API-slutpunkten och start för [Azure Active Directory Identity Protection](./overview-identity-protection.md) -API: er. Det finns tre API: er som visar information om riskfyllda användare och inloggningar. Med det första API: et, **riskDetection**, kan du fråga Microsoft Graph om du vill visa en lista över både användare och inloggning länkade risk identifieringar och tillhör ande information om identifieringen. Med det andra API: et, **riskyUsers**, kan du fråga Microsoft Graph om du vill ha information om användarnas identitets skydd som har identifierats som risk Med det tredje API: t **loggar** du kan du fråga Microsoft Graph om du vill veta mer om Azure AD-inloggningar med särskilda egenskaper som rör risk tillstånd, information och nivå. 

Den här artikeln hjälper dig att komma igång med att ansluta till Microsoft Graph och fråga dessa API: er. För en djupgående introduktion, fullständig dokumentation och åtkomst till Graph Explorer, se [Microsoft Graph webbplats](https://graph.microsoft.io/) eller den speciella referens dokumentationen för dessa API: er:

* [riskDetection-API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers-API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [signIn-API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Anslut till Microsoft Graph

Det finns fyra steg för att komma åt identitets skydds data via Microsoft Graph:

- [Hämta ditt domän namn](#retrieve-your-domain-name)
- [Skapa en ny app-registrering](#create-a-new-app-registration)
- [Konfigurera API-behörigheter](#configure-api-permissions)
- [Konfigurera en giltig autentiseringsuppgift](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Hämta ditt domän namn 

1. Logga in på [Azure-portalen](https://portal.azure.com).  
1. Bläddra till **Azure Active Directory**  >  **anpassade domän namn**. 
1. Anteckna `.onmicrosoft.com` domänen. du kommer att behöva den här informationen i ett senare steg.

### <a name="create-a-new-app-registration"></a>Skapa en ny app-registrering

1. I Azure Portal bläddrar du till **Azure Active Directory**  >  **Appregistreringar**.
1. Välj **ny registrering**.
1. Utför följande steg på sidan **skapa** :
   1. I text rutan **namn** anger du ett namn för ditt program (t. ex. Azure AD-API för risk identifiering).
   1. Under **typer av konto typer som stöds** väljer du den typ av konton som ska använda API: erna.
   1. Välj **Register** (Registrera).
1. Kopiera **program-ID: t**.

### <a name="configure-api-permissions"></a>Konfigurera API-behörigheter

1. Välj **API-behörigheter** från det **program** som du har skapat.
1. På sidan **konfigurerade behörigheter** , i verktygsfältet högst upp, klickar du på **Lägg till en behörighet**.
1. På sidan **Lägg till API-åtkomst** klickar du på **Välj ett API**.
1. På sidan **Välj ett API** väljer du **Microsoft Graph** och klickar sedan på **Välj**.
1. På sidan **begär API-behörigheter** : 
   1. Välj **Programbehörigheter**.
   1. Markera kryss rutorna bredvid `IdentityRiskEvent.Read.All` och `IdentityRiskyUser.Read.All` .
   1. Välj **Lägg till behörigheter**.
1. Välj **bevilja administrativt godkännande för domänen** 

### <a name="configure-a-valid-credential"></a>Konfigurera en giltig autentiseringsuppgift

1. Välj **certifikat & hemligheter** från det **program** som du har skapat.
1. Under **klient hemligheter** väljer du **ny klient hemlighet**.
   1. Ge klient hemligheten en **Beskrivning** och ange tids perioden för förfallo tid enligt organisationens principer.
   1. Välj **Lägg till**.

   > [!NOTE]
   > Om du förlorar den här nyckeln måste du gå tillbaka till det här avsnittet och skapa en ny nyckel. Behåll den här nyckeln till en hemlighet: vem som helst som har behörighet att komma åt dina data.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autentisera till Microsoft Graph och fråga identitets risk identifierings-API: et

I det här läget bör du ha:

- Namnet på din klients domän
- Program-ID (klient) 
- Klient hemlighet eller certifikat 

Du autentiserar genom att skicka en post-begäran till `https://login.microsoft.com` med följande parametrar i texten:

- grant_type: "**client_credentials**"
- klusterresursen `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Om det lyckas returnerar den här begäran en autentiseringstoken.  
Om du vill anropa API: et skapar du ett sidhuvud med följande parameter:

```
`Authorization`="<token_type> <access_token>"
```

Vid autentisering kan du hitta tokentyp och åtkomsttoken i den returnerade token.

Skicka den här rubriken som en begäran till följande API-URL: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

Svaret, om det lyckas, är en samling identitets risk identifieringar och tillhör ande data i OData JSON-format, som kan tolkas och hanteras på det sätt som visas.

### <a name="sample"></a>Exempel

Det här exemplet visar hur du använder en delad hemlighet för att autentisera. I en produktions miljö är det vanligt vis att lagra hemligheter i koden. Organisationer kan använda hanterade identiteter för Azure-resurser för att skydda dessa autentiseringsuppgifter. Mer information om hanterade identiteter finns i artikeln [Vad är hanterade identiteter för Azure-resurser](../managed-identities-azure-resources/overview.md).

Här är exempel kod för att autentisera och anropa API med hjälp av PowerShell.  
Lägg bara till klient-ID, hemlig nyckel och klient domän.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Fråga API: erna

Dessa tre API: er ger en mängd möjligheter att hämta information om riskfyllda användare och inloggningar i din organisation. Nedan visas några vanliga användnings fall för dessa API: er och de associerade exempel förfrågningarna. Du kan köra dessa frågor med hjälp av exempel koden ovan eller med hjälp av [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Hämta alla identifieringar av offline-risk (riskDetection-API)

Med identitets skydds principer för inloggnings risker kan du tillämpa villkor när risken upptäcks i real tid. Men vad händer om identifieringar som upptäcks offline? Du kan fråga riskDetection-API: et för att förstå vilka identifieringar som har inträffat offline och därmed inte har utlöst inloggnings risk principen.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Hämta alla användare som har godkänt en MFA-utmaning som har utlösts av riskfyllda inloggnings principer (riskyUsers-API)

För att förstå de riskbaserade principerna för att påverka identiteter som finns i din organisation, kan du fråga alla användare som har godkänt en MFA-utmaning som har utlösts av en risk inloggnings princip. Den här informationen kan hjälpa dig att förstå vilka användare som har identitets skydd som kan ha identifierats som risk och vilka av dina legitima användare som kan utföra åtgärder som AI bedömer som en risk.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Nästa steg

Grattis, du har precis gjort ditt första samtal till Microsoft Graph!  
Nu kan du ställa frågor till identifieringar av identitets risker och använda data som du inte ser.

Om du vill veta mer om Microsoft Graph och hur du skapar program med hjälp av Graph API kan du läsa [dokumentationen](/graph/overview) och mycket mer på [Microsoft Graph-webbplatsen](https://developer.microsoft.com/graph). 

Relaterad information finns i:

- [Identitetsskydd för Azure Active Directory](./overview-identity-protection.md)
- [Typer av risk identifieringar som upptäckts av Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Översikt över Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection tjänst roten](/graph/api/resources/identityprotectionroot)
