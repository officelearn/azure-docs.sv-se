---
title: Microsoft Graph-API för Azure Active Directory Identity Protection
description: Lär dig hur du frågar Microsoft Graph risk identifieringar och tillhör ande information från Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 324737611d2d05411012050fcf7140bee48d35b0
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85505842"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Kom igång med Azure Active Directory Identity Protection och Microsoft Graph

Microsoft Graph är Microsoft Unified API-slutpunkten och start för [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) -API: er. Det finns fyra API: er som visar information om riskfyllda användare och inloggningar. Med det första API: et, **riskDetection**, kan du fråga Microsoft Graph om du vill visa en lista över både användare och inloggning länkade risk identifieringar och tillhör ande information om identifieringen. Med det andra API: et, **riskyUsers**, kan du fråga Microsoft Graph om du vill ha information om användarnas identitets skydd som har identifierats som risk Med det tredje API: t **loggar**du kan du fråga Microsoft Graph om du vill veta mer om Azure AD-inloggningar med särskilda egenskaper som rör risk tillstånd, information och nivå. Med det fjärde API: et, **identityRiskEvents**, kan du fråga Microsoft Graph om du vill visa en lista över [risk identifieringar](../reports-monitoring/concept-risk-events.md) och tillhör ande information. IdentityRiskEvents-API: et är inaktuellt den 10 januari 2020; Vi rekommenderar att du använder **riskDetections** -API: et i stället. Den här artikeln hjälper dig att komma igång med att ansluta till Microsoft Graph och fråga dessa API: er. För en djupgående introduktion, fullständig dokumentation och åtkomst till Graph Explorer, se [Microsoft Graph webbplats](https://graph.microsoft.io/) eller den speciella referens dokumentationen för dessa API: er:

* [riskDetection-API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers-API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [inloggnings-API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Anslut till Microsoft Graph

Det finns fyra steg för att komma åt identitets skydds data via Microsoft Graph:

1. Hämta ditt domän namn.
2. Skapa en ny app-registrering. 
3. Använd den här hemligheten och några andra informations delar för att autentisera till Microsoft Graph, där du får en autentiseringstoken. 
4. Använd denna token för att göra förfrågningar till API-slutpunkten och få tillbaka identitets skydds data.

Innan du börjar måste du ha:

* Administratörs behörighet för att skapa programmet i Azure AD
* Namnet på klient organisationens domän (till exempel contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Hämta ditt domän namn 

1. [Logga in](https://portal.azure.com) på Azure Portal som administratör. 
1. Klicka på **Active Directory**i det vänstra navigerings fönstret. 

   ![Skapa ett program](./media/howto-identity-protection-graph-api/41.png)

1. I avsnittet **Hantera** klickar du på **Egenskaper**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/42.png)

1. Kopiera ditt domän namn.

## <a name="create-a-new-app-registration"></a>Skapa en ny app-registrering

1. Klicka på **Appregistreringar**i avsnittet **hantera** på sidan **Active Directory** .

   ![Skapa ett program](./media/howto-identity-protection-graph-api/42.png)

1. Klicka på **ny program registrering**i menyn högst upp.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/43.png)

1. Utför följande steg på sidan **skapa** :

   ![Skapa ett program](./media/howto-identity-protection-graph-api/44.png)

   1. I text rutan **namn** anger du ett namn för ditt program (t. ex. Azure AD-API application för farlighets igenkänning).

   1. Som **typ**väljer du **webb program och/eller webb-API**.

   1. I text rutan **inloggnings-URL** skriver du `http://localhost` .

   1. Klicka på **Skapa**.
1. Öppna sidan **Inställningar** i listan program genom att klicka på din nyligen skapade app-registrering. 
1. Kopiera **program-ID: t**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Ge ditt program behörighet att använda API: et

1. På sidan **Inställningar** klickar du på **nödvändiga behörigheter**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/15.png)

1. Klicka på **Lägg till**i verktygsfältet högst upp på sidan **behörigheter som krävs** .

   ![Skapa ett program](./media/howto-identity-protection-graph-api/16.png)

1. På sidan **Lägg till API-åtkomst** klickar du på **Välj ett API**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/17.png)

1. På sidan **Välj ett API** väljer du **Microsoft Graph**och klickar sedan på **Välj**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/18.png)

1. På sidan **Lägg till API-åtkomst** klickar du på **Välj behörigheter**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/19.png)

1. På sidan **Aktivera åtkomst** klickar du på **Läs all information om identitets risker**och klickar sedan på **Välj**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/20.png)

1. På sidan **Lägg till API-åtkomst** klickar du på **Slutför**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/21.png)

1. På sidan **behörigheter som krävs** klickar du på **bevilja behörigheter**och sedan på **Ja**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Hämta en åtkomstnyckel

1. På sidan **Inställningar** klickar du på **nycklar**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/23.png)

1. Utför följande steg på sidan **nycklar** :

   ![Skapa ett program](./media/howto-identity-protection-graph-api/24.png)

   1. I text rutan **nyckel Beskrivning** skriver du en beskrivning (till exempel *identifiering av Azure AD-risk*).
   1. Som **varaktighet**väljer du **i ett år**.
   1. Klicka på **Spara**.
   1. Kopiera nyckelvärdet och klistra in det på en säker plats.   
   
   > [!NOTE]
   > Om du förlorar den här nyckeln måste du gå tillbaka till det här avsnittet och skapa en ny nyckel. Behåll den här nyckeln till en hemlighet: vem som helst som har behörighet att komma åt dina data.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autentisera till Microsoft Graph och fråga identitets risk identifierings-API: et

I det här läget bör du ha:

- Namnet på din klients domän
- Klient-ID 
- Nyckeln 

Du autentiserar genom att skicka en post-begäran till `https://login.microsoft.com` med följande parametrar i texten:

- grant_type: "**client_credentials**"
- klusterresursen`https://graph.microsoft.com`
- client_id:\<your client ID\>
- client_secret:\<your key\>

Om det lyckas returnerar detta en autentiseringstoken.  
Om du vill anropa API: et skapar du ett sidhuvud med följande parameter:

```
`Authorization`="<token_type> <access_token>"
```

Vid autentisering kan du hitta tokentyp och åtkomsttoken i den returnerade token.

Skicka den här rubriken som en begäran till följande API-URL:`https://graph.microsoft.com/beta/identityRiskEvents`

Svaret, om det lyckas, är en samling identitets risk identifieringar och tillhör ande data i OData JSON-format, som kan tolkas och hanteras på det sätt som visas.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
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

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
- [Typer av risk identifieringar som upptäckts av Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Översikt över Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection tjänst roten](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)
