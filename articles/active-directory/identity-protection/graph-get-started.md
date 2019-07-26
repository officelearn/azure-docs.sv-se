---
title: Microsoft Graph för Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du frågar Microsoft Graph efter en lista över risk händelser och tillhör ande information från Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f939811bec312baa1f4c37f0f915d2e881121af
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334075"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Kom igång med Azure Active Directory Identity Protection och Microsoft Graph

Microsoft Graph är Microsoft Unified API-slutpunkten och start för [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) -API: er. Det finns tre API: er som visar information om riskfyllda användare och inloggningar. Med det första API: et, **identityRiskEvents**, kan du fråga Microsoft Graph om du vill ha en lista över [risk händelser](../reports-monitoring/concept-risk-events.md) och tillhör ande information. Med det andra API: et, **riskyUsers**, kan du fråga Microsoft Graph om du vill ha information om användarnas identitets skydd som har identifierats som risk Med det tredje API: t **loggar**du kan du fråga Microsoft Graph om du vill veta mer om Azure AD-inloggningar med särskilda egenskaper som rör risk tillstånd, information och nivå. Den här artikeln hjälper dig att komma igång med att ansluta till Microsoft Graph och fråga dessa API: er. För en djupgående introduktion, fullständig dokumentation och åtkomst till Graph Explorer, se [Microsoft Graph webbplats](https://graph.microsoft.io/) eller den speciella referens dokumentationen för dessa API: er:

* [identityRiskEvents-API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers-API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [inloggnings-API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)

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

   ![Skapa ett program](./media/graph-get-started/41.png)

1. I avsnittet **Hantera** klickar du på **Egenskaper**.

   ![Skapa ett program](./media/graph-get-started/42.png)

1. Kopiera ditt domän namn.

## <a name="create-a-new-app-registration"></a>Skapa en ny appregistrering

1. Klicka på **Appregistreringar**i avsnittet **hantera** på sidan **Active Directory** .

   ![Skapa ett program](./media/graph-get-started/42.png)

1. Klicka på **ny program registrering**i menyn högst upp.

   ![Skapa ett program](./media/graph-get-started/43.png)

1. Utför följande steg på sidan **skapa** :

   ![Skapa ett program](./media/graph-get-started/44.png)

   1. I text rutan **namn** anger du ett namn för ditt program (till exempel: API-program för AADIP-risk event).

   1. Som **typ**väljer du **webb program och/eller webb-API**.

   1. I text rutan **inloggnings-URL** skriver `http://localhost`du.

   1. Klicka på **Skapa**.
1. Öppna sidan **Inställningar** i listan program genom att klicka på din nyligen skapade app-registrering. 
1. Kopiera **program-ID: t**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Ge ditt program behörighet att använda API: et

1. På sidan **Inställningar** klickar du på **nödvändiga behörigheter**.

   ![Skapa ett program](./media/graph-get-started/15.png)

1. Klicka på **Lägg till**i verktygsfältet högst upp på sidan **behörigheter som krävs** .

   ![Skapa ett program](./media/graph-get-started/16.png)

1. På sidan **Lägg till API-åtkomst** klickar du på **Välj ett API**.

   ![Skapa ett program](./media/graph-get-started/17.png)

1. På sidan **Välj ett API** väljer du **Microsoft Graph**och klickar sedan på **Välj**.

   ![Skapa ett program](./media/graph-get-started/18.png)

1. På sidan **Lägg till API-åtkomst** klickar du på **Välj behörigheter**.

   ![Skapa ett program](./media/graph-get-started/19.png)

1. På sidan **Aktivera åtkomst** klickar du på **Läs all information om identitets risker**och klickar sedan på **Välj**.

   ![Skapa ett program](./media/graph-get-started/20.png)

1. På sidan **Lägg till API-åtkomst** klickar du på **Slutför**.

   ![Skapa ett program](./media/graph-get-started/21.png)

1. På sidan **behörigheter som krävs** klickar du på **bevilja behörigheter**och sedan på **Ja**.

   ![Skapa ett program](./media/graph-get-started/22.png)

## <a name="get-an-access-key"></a>Hämta en åtkomstnyckel

1. På sidan **Inställningar** klickar du på **nycklar**.

   ![Skapa ett program](./media/graph-get-started/23.png)

1. Utför följande steg på sidan **nycklar** :

   ![Skapa ett program](./media/graph-get-started/24.png)

   1. I text rutan **nyckel Beskrivning** skriver du en beskrivning (till exempel *AADIP risk händelse*).
   1. Som **varaktighet**väljer du **i ett år**.
   1. Klicka på **Spara**.
   1. Kopiera nyckelvärdet och klistra in det på en säker plats.   
   
   > [!NOTE]
   > Om du förlorar den här nyckeln måste du gå tillbaka till det här avsnittet och skapa en ny nyckel. Behåll den här nyckeln till en hemlighet: vem som helst som har behörighet att komma åt dina data.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autentisera till Microsoft Graph och skicka frågor till API: et för identitets risk händelser

I det här läget bör du ha:

- Namnet på din klients domän
- Klient-ID 
- Nyckeln 

Du autentiserar genom att skicka en post `https://login.microsoft.com` -begäran till med följande parametrar i texten:

- grant_type: "**client_credentials**"
- klusterresursen`https://graph.microsoft.com`
- client_id: \<ditt klient-ID\>
- client_secret: \<din nyckel\>

Om det lyckas returnerar detta en autentiseringstoken.  
Om du vill anropa API: et skapar du ett sidhuvud med följande parameter:

```
`Authorization`=”<token_type> <access_token>"
```

Vid autentisering kan du hitta tokentyp och åtkomsttoken i den returnerade token.

Skicka den här rubriken som en begäran till följande API-URL:`https://graph.microsoft.com/beta/identityRiskEvents`

Svaret, om det lyckas, är en samling identitets risk händelser och tillhör ande data i OData JSON-format, som kan tolkas och hanteras på det sätt som visas.

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

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Hämta händelser med hög risk och medelhög risk (identityRiskEvents-API)

Medel-och hög risk händelser representerar de som kan ha möjlighet att utlösa inloggnings-eller användar risk principer för identitets skydd. Eftersom de har medelhög eller hög sannolikhet för att användaren som försöker logga in inte är den legitima identitets ägaren, bör det vara en prioritet att åtgärda dessa händelser. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Hämta alla användare som har godkänt en MFA-utmaning som har utlösts av riskfyllda inloggnings principer (riskyUsers-API)

För att förstå de riskbaserade principerna för att påverka identiteter som är baserade på din organisation kan du fråga alla användare som har godkänt en MFA-utmaning som har utlösts av en risk inloggnings princip. Den här informationen kan hjälpa dig att förstå vilka användare som har identitets skydd som kan ha identifierats som risk och vilka av dina legitima användare som kan utföra åtgärder som AI bedömer som en risk.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Hämta alla riskfyllda inloggningar för en speciell användare (inloggnings-API)

När du tror att en användare kan ha komprometterats kan du bättre förstå statusen för deras risk genom att hämta alla deras riskfyllda inloggningar. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Nästa steg

Grattis, du har precis gjort ditt första samtal till Microsoft Graph!  
Nu kan du fråga efter identitets risk händelser och använda data som du inte kan se.

Om du vill veta mer om Microsoft Graph och hur du skapar program med hjälp av Graph API kan du läsa [dokumentationen](https://docs.microsoft.com/graph/overview) och mycket mer på [Microsoft Graph-webbplatsen](https://developer.microsoft.com/graph). 

Relaterad information finns i:

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
- [Typer av risk händelser som identifieras av Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Översikt över Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection tjänst roten](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
